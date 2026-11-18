# 0001-6 — Selección del estilo arquitectónico del periódico digital

* Status: proposed
* Date: 2026-11-18

## Context and Problem Statement

El periódico digital debe atender peticiones desde un front-end web responsivo para PC y tablet, tal como establece el Anexo I. El RF01 recoge la atención de las peticiones de los clientes y el RD1 fija HTTP/REST como protocolo de comunicación. El Anexo también establece dos bases de datos PostgreSQL 19 separadas. La organización del acceso a esas bases se trata en el MADR [0005-6](./0005-6%20%E2%80%94%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md). El sistema contempla además suscripciones, pagos mediante Stripe y estadísticas para administradores y redactores.

El Anexo I establece qué debe ofrecer el sistema, pero no cómo distribuir internamente esas responsabilidades. La decisión pendiente es elegir una estructura para el conjunto, no escoger otra pasarela, modificar el número de bases de datos ni añadir funcionalidades. La elección debe dejar una arquitectura que pueda representarse en UML mediante paquetes y clases, con dependencias entre paquetes que sigan una dirección clara.

## Decision Drivers

* **RF01 — Gestión de peticiones del periódico digital:** el sistema debe atender las peticiones recibidas desde el front-end web, comunicarse mediante HTTP/REST y contar con las dos bases PostgreSQL 19 indicadas.
* **RD1 — Comunicación HTTP/REST:** las peticiones del front-end web deben realizarse mediante HTTP/REST.

Del RF01, esta decisión toma como punto de partida la atención de peticiones desde el front-end web. Las dos bases de datos son una condición del sistema y la forma de acceder a ellas se decide en el MADR [0005-6](./0005-6%20%E2%80%94%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md).

## Considered Options

* **Opción A — Cliente-servidor con back-end monolítico modular por capas.** Un front-end web llama a una entrada HTTP/REST. Dentro de un único back-end se diferencian la entrada, la lógica de aplicación y los adaptadores de integración y persistencia.
* **Opción B — Cliente-servidor con back-end dividido en microservicios.** El front-end consume servicios a través de HTTP/REST y las responsabilidades del sistema se distribuyen entre procesos independientes que colaboran entre sí.

## Decision Outcome

Chosen option: "Opción A — Cliente-servidor con back-end monolítico modular por capas", because permite atender las peticiones del periódico mediante una frontera HTTP/REST y repartir las responsabilidades en paquetes reconocibles, sin introducir la coordinación entre servicios separados que el Anexo no exige.

### Consequences

* Good, because el front-end no necesita conocer los detalles de Stripe ni de las conexiones PostgreSQL; estas responsabilidades quedan en el back-end.
* Good, because un único back-end permite mantener separadas suscripciones, facturación y estadísticas por paquetes sin necesitar comunicaciones adicionales entre servicios.
* Bad, because una incidencia del proceso del back-end puede afectar a varias funciones a la vez y la separación interna depende de que se respeten los límites de los paquetes.

### Confirmation

Seguiremos una petición de lectura para comprobar que el cliente se comunica por HTTP/REST con un único back-end y que la recepción de la petición, la lógica de aplicación y el acceso a los datos tienen responsabilidades separadas.

## Pros and Cons of the Options

### Opción A — Cliente-servidor con back-end monolítico modular por capas

* Good, because permite separar claramente la recepción de peticiones de la lógica que gestiona suscripciones y cobros.
* Good, because la capa de integración y persistencia concentra el acceso a sistemas externos y a los datos, de modo que la presentación web queda al margen de esos detalles.
* Bad, because el despliegue es conjunto y un problema en el back-end puede repercutir sobre distintos módulos.
* Bad, because si los paquetes se diseñan sin límites claros, la separación por capas podría quedarse en una división solo nominal.

### Opción B — Cliente-servidor con back-end dividido en microservicios

* Good, because podría aislar el despliegue de estadísticas del de pagos si hubiera necesidades operativas diferentes.
* Good, because hace explícitos los límites de responsabilidad entre servicios independientes.
* Bad, because añade llamadas entre servicios y coordinación de fallos para operaciones que el Anexo no pide distribuir.

## Links

* Requisito(s) motivador(es): RF01, RD1 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye al [MADR 0001-4](../Semana%202/0001-4-estilo-arquitectonico.md), según consta en la [anulación 0001-5](./0001-5%20Anulamos%20Selecci%C3%B3n%20del%20estilo%20arquitect%C3%B3nico%20del%20peri%C3%B3dico%20digital.md).
* Decisiones relacionadas: MADR [0002-6](./0002-6%20%E2%80%94%20Diferenciaci%C3%B3n%20de%20clientes%20y%20medici%C3%B3n%20de%20uso%20para%20las%20estad%C3%ADsticas.md), [0003-6](./0003-6%20%E2%80%94%20Gesti%C3%B3n%20an%C3%B3nima%20de%20las%20suscripciones%20a%20noticias%20y%20sus%20bajas.md), [0004-6](./0004-6%20%E2%80%94%20Organizaci%C3%B3n%20de%20la%20facturaci%C3%B3n%20y%20el%20cobro%20de%20suscripciones.md) y [0005-6](./0005-6%20%E2%80%94%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md), que concretan responsabilidades dentro de la estructura seleccionada, sin reutilizar aquí sus requisitos como drivers.
