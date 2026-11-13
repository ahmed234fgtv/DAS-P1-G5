# 0001-2 — Selección del estilo arquitectónico del periódico digital

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

El periódico digital debe atender peticiones de los clientes desde un front-end web para PC y tablet. El RF01 describe este funcionamiento general y recoge la comunicación HTTP/REST y la existencia de dos bases de datos PostgreSQL 19 separadas. El RD1 fija igualmente HTTP/REST como protocolo para las peticiones. El sistema también contempla suscripciones, pagos mediante Stripe y estadísticas para administradores y redactores.

El Anexo I establece qué debe ofrecer el sistema, pero no cómo distribuir internamente esas responsabilidades. La decisión pendiente es elegir una estructura para el conjunto, no escoger otra pasarela, modificar el número de bases de datos ni añadir funcionalidades. La elección debe dejar una arquitectura que pueda representarse en UML mediante paquetes y clases, con dependencias entre paquetes que sigan una dirección clara.

## Decision Drivers

* **RF01 — Gestión de peticiones del periódico digital:** el sistema debe atender las peticiones recibidas desde el front-end web, comunicarse mediante HTTP/REST y contar con las dos bases PostgreSQL 19 indicadas.
* **RD1 — Comunicación HTTP/REST:** las peticiones del front-end web deben realizarse mediante HTTP/REST.

## Considered Options

* **Opción A — Cliente-servidor con back-end monolítico modular por capas.** Un front-end web llama a una entrada HTTP/REST. Dentro de un único back-end se diferencian la entrada, la lógica de aplicación y los adaptadores de integración y persistencia.
* **Opción B — Cliente-servidor con back-end dividido en microservicios.** El front-end consume servicios a través de HTTP/REST y las responsabilidades del sistema se distribuyen entre procesos independientes que colaboran entre sí.

## Decision Outcome

Chosen option: "Opción A — Cliente-servidor con back-end monolítico modular por capas", because permite atender las peticiones del periódico mediante una frontera HTTP/REST y repartir las responsabilidades en paquetes reconocibles, sin introducir la coordinación entre servicios separados que el Anexo no exige. La existencia de dos bases de datos no obliga a dividir el back-end en dos procesos.

### Consequences

* Good, because el front-end no necesita conocer los detalles de Stripe ni de las conexiones PostgreSQL; estas responsabilidades quedan en el back-end.
* Good, because un único back-end permite mantener separadas suscripciones, facturación y estadísticas por paquetes sin necesitar comunicaciones adicionales entre servicios.
* Bad, because una incidencia del proceso del back-end puede afectar a varias funciones a la vez y la separación interna depende de que se respeten los límites de los paquetes.

### Confirmation

En el UML inicial deben distinguirse el paquete o elemento del front-end web, la entrada HTTP/REST, la lógica de aplicación y los adaptadores de integración y persistencia. Las dependencias se dibujarán entre paquetes, sin ciclos ni conexiones directas del front-end a Stripe o PostgreSQL. Se representarán las dos bases de datos exigidas y las clases podrán situarse en los paquetes correspondientes, sin establecer relaciones entre clases, de acuerdo con el criterio indicado por el profesor. No es necesario definir todavía el detalle interno de todos los módulos.

## Pros and Cons of the Options

### Opción A — Cliente-servidor con back-end monolítico modular por capas

* Good, because permite separar claramente la recepción de peticiones de la lógica que gestiona suscripciones y cobros.
* Good, because los adaptadores concentran la integración con Stripe y las dos conexiones PostgreSQL, evitando que la presentación web dependa de ellas.
* Bad, because el despliegue es conjunto y un problema en el back-end puede repercutir sobre distintos módulos.
* Bad, because si los paquetes se diseñan sin límites claros, la separación por capas podría quedarse en una división solo nominal.

### Opción B — Cliente-servidor con back-end dividido en microservicios

* Good, because podría aislar el despliegue de estadísticas del de pagos si hubiera necesidades operativas diferentes.
* Good, because hace explícitos los límites de responsabilidad entre servicios independientes.
* Bad, because añade llamadas entre servicios y coordinación de fallos para operaciones que el Anexo no pide distribuir.
* Bad, because complica el acceso controlado a las dos bases prescritas, porque habría que delimitar qué servicio gestiona cada dato sin alterar esa restricción.

## Links

* Requisito(s) motivador(es): RF01, RD1 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0001](./0001-estilo-arquitectonico.md). La anulación anterior queda registrada como 0001-1.
* Decisiones relacionadas: MADR 0002-2 a 0008-2, que concretan responsabilidades dentro de la estructura seleccionada, sin reutilizar aquí sus requisitos como drivers.
