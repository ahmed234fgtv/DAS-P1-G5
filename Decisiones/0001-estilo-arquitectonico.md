# 0001 — Selección del estilo arquitectónico del periódico digital

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I describe un periódico digital con un front-end web para PC y tablet, un canal de suscripciones a noticias, un módulo de facturación y pagos mediante Stripe, estadísticas de uso para administradores y redactores y dos bases de datos PostgreSQL 19 diferenciadas. El protocolo HTTP/REST entre el cliente web y el sistema está fijado, pero el Anexo no prescribe cómo organizar internamente el back-end.

La decisión consiste en seleccionar **la estructura arquitectónica del conjunto**: cómo separar el cliente web, las responsabilidades de aplicación y las dependencias de Stripe y de la persistencia, de modo que los siguientes MADR puedan concretar clases y paquetes sin introducir dependencias circulares. No se está eligiendo un lenguaje, framework, mecanismo de despliegue concreto ni nuevas funcionalidades.

## Decision Drivers

* **RD1 — Comunicación HTTP/REST:** las peticiones del front-end web deben realizarse mediante HTTP/REST.

Los demás elementos del Anexo se utilizan aquí como **contexto del sistema**, no como drivers adicionales; se reservan para las decisiones que abordan su diseño específico.

## Considered Options

* **0001-1 — Cliente-servidor con back-end monolítico modular organizado en capas.** El cliente web consume una API HTTP/REST; un único back-end separa la entrada HTTP, la lógica de aplicación y los adaptadores de integración/persistencia.
* **0001-2 — Cliente-servidor con back-end de microservicios.** El cliente web consume servicios HTTP/REST; las capacidades funcionales se reparten entre servicios independientes, cada uno con su propia lógica y puntos de integración.

Ambas alternativas pueden respetar HTTP/REST, usar Stripe y trabajar con las dos bases de datos prescritas. La comparación se centra en la **distribución de responsabilidades y límites de ejecución**, no en eliminar elementos obligatorios.

## Decision Outcome

Chosen option: "0001-1 — Cliente-servidor con back-end monolítico modular organizado en capas", because materializa de forma explícita la comunicación HTTP/REST exigida y permite separar las responsabilidades del periódico en paquetes bien delimitados, sin exigir coordinación entre varios servicios desplegables que el Anexo no necesita. La modularidad es **interna al back-end**: no implica mezclar la lógica del periódico con la interfaz web.

La propuesta queda pendiente de la revisión de Jordi y del contraste posterior con los ASC. No se considera aceptada por el grupo mientras figure como `proposed`.

### Consequences

* Good, because la separación entre el cliente web, la lógica de aplicación y las integraciones permite ubicar sin ambigüedad las responsabilidades de suscripciones, pagos y estadísticas en los diagramas sucesivos.
* Bad, because el back-end comparte un mismo despliegue lógico: un fallo o un crecimiento de carga en un módulo puede afectar a los demás y obliga a mantener límites internos claros.
* La elección no obliga a utilizar una SPA, un framework, un lenguaje ni un mecanismo concreto de despliegue.

### Confirmation

La arquitectura UML inicial deberá permitir comprobar que existe un **front-end web** que se comunica con una **entrada HTTP/REST** del back-end y que, dentro de este, las responsabilidades de aplicación y las de integración/persistencia están separadas en paquetes. Las dependencias entre paquetes deben seguir un sentido coherente, sin ciclos ni referencias directas de la presentación web a Stripe o a las clases de acceso a PostgreSQL. El diagrama no tiene por qué fijar aún todos los detalles internos de los módulos, que se concretarán en las decisiones posteriores.

## Pros and Cons of the Options

### 0001-1 — Cliente-servidor con back-end monolítico modular organizado en capas

* Good, because ofrece una frontera HTTP/REST clara entre el cliente para PC/tablet y las operaciones de suscripciones, pagos y estadísticas.
* Good, because el UML puede mostrar dependencias dirigidas desde controladores hacia servicios y de estos hacia interfaces de persistencia/integración, sin acoplar las clases funcionales a Stripe o PostgreSQL.
* Good, because los cambios en las reglas de facturación se mantienen dentro del back-end y no requieren coordinar versiones de varios servicios desplegados por separado.
* Bad, because un fallo del proceso de back-end puede afectar simultáneamente a varias funcionalidades; el diseño modular por sí solo no proporciona aislamiento operativo.
* Bad, because una mala aplicación de los límites de capas podría crear una clase de servicio que concentre suscripciones, pagos y estadísticas; habrá que revisar la distribución de responsabilidades en UML.

### 0001-2 — Cliente-servidor con back-end de microservicios

* Good, because permitiría desplegar y escalar por separado funcionalidades con cargas distintas, como consultas estadísticas y gestión de pagos, si ese objetivo llegara a justificarse.
* Good, because los límites entre servicios serían explícitos mediante contratos de comunicación, dificultando las dependencias directas entre sus clases internas.
* Bad, because añade comunicación entre servicios, gestión de errores de red y coordinación de datos para funcionalidades que deben relacionarse, aunque el Anexo no exige independencia de despliegue ni escalabilidad específica.
* Bad, because la existencia de solo dos bases de datos prescritas obliga a justificar cuidadosamente qué servicio accede a cada una; dividir por funcionalidad no resuelve automáticamente esa responsabilidad.

## Links

* Requisito motivador: RD1 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Relación: los MADR posteriores desarrollan los paquetes y clases de esta arquitectura.
