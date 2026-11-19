# 0001-8 — Selección del estilo arquitectónico del periódico digital

* Status: proposed
* Date: 2026-11-19

## Context and Problem Statement

El periódico digital debe atender peticiones desde un front-end web responsivo para PC y tablet, tal como establece el Anexo I. El RF01 recoge la atención de las peticiones de los clientes y el RD1 fija HTTP/REST como protocolo de comunicación. El Anexo también establece dos bases de datos PostgreSQL 19 separadas. La organización del acceso a esas bases se trata en el [MADR 0005-8](<./0005-8 — Organización del acceso a las dos bases de datos PostgreSQL 19.md>). El sistema contempla además suscripciones, pagos mediante Stripe y estadísticas para administradores y redactores.

El problema de diseño consiste en elegir cómo organizar el back-end del periódico digital. Se comparan dos estilos según el grado de independencia entre sus partes y el coste de coordinarlas cuando una petición necesita varias funciones.

## Decision Drivers

* **RF01 — Gestión de peticiones del periódico digital:** el sistema debe atender las peticiones recibidas desde el front-end web, comunicarse mediante HTTP/REST y contar con las dos bases PostgreSQL 19 indicadas.
* **RD1 — Comunicación HTTP/REST:** las peticiones del front-end web deben realizarse mediante HTTP/REST.

Del RF01, esta decisión toma como punto de partida la atención de peticiones desde el front-end web. Las dos bases de datos son una condición del sistema y la forma de acceder a ellas se decide en el [MADR 0005-8](<./0005-8 — Organización del acceso a las dos bases de datos PostgreSQL 19.md>).

## Considered Options

* **Opción A — Cliente-servidor con back-end monolítico modular por capas.** El periódico funciona como una única aplicación de servidor, organizada por responsabilidades. La comunicación con el front-end se realiza mediante HTTP/REST.
* **Opción B — Cliente-servidor con back-end dividido en microservicios.** El front-end se comunica mediante HTTP/REST y las funciones del periódico se distribuyen entre servicios que se pueden desplegar de forma independiente.

## Decision Outcome

Chosen option: "Opción A — Cliente-servidor con back-end monolítico modular por capas", because permite diferenciar las responsabilidades del periódico dentro de una sola aplicación. La coordinación entre funciones se mantiene dentro del back-end y se evita el coste de coordinar servicios desplegados de forma independiente.

### Consequences

* Good, because las funciones del periódico se pueden organizar por responsabilidades dentro de un único despliegue.
* Bad, because una incidencia en el proceso del back-end puede afectar a varias funciones a la vez.

### Confirmation

Comprobaremos que una petición de lectura del front-end se atiende mediante HTTP/REST en un único back-end.

## Pros and Cons of the Options

### Opción A — Cliente-servidor con back-end monolítico modular por capas

* Good, because las funciones del periódico pueden colaborar dentro de la misma aplicación sin comunicación entre procesos independientes.
* Bad, because actualizar una función, como la consulta de estadísticas, implica desplegar de nuevo el conjunto del back-end.

### Opción B — Cliente-servidor con back-end dividido en microservicios

* Good, because permitiría desplegar los cambios en estadísticas de forma independiente de los cambios en el cobro.
* Bad, because las operaciones que necesiten varias funciones requieren comunicación entre servicios y gestión de fallos entre ellos.

## Links

* Requisito(s) motivador(es): RF01, RD1 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye al [MADR 0001-6](<./0001-6 — Selección del estilo arquitectónico del periódico digital.md>), según consta en la [anulación 0001-7](<./0001-7 Anulamos Selección del estilo arquitectónico del periódico digital.md>).
* Decisiones relacionadas: [MADR 0002-6](<./0002-6 — Diferenciación de clientes y medición de uso para las estadísticas.md>), [MADR 0003-6](<./0003-6 — Gestión anónima de las suscripciones a noticias y sus bajas.md>), [MADR 0004-8](<./0004-8 — Organización de la facturación y el cobro de suscripciones.md>) y [MADR 0005-8](<./0005-8 — Organización del acceso a las dos bases de datos PostgreSQL 19.md>).
