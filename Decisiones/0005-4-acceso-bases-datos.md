# 0005-4 — Organización del acceso a las dos bases de datos PostgreSQL 19

* Status: proposed
* Date: 2026-11-14

## Context and Problem Statement

El RD3 establece que el back-end debe contar con dos bases de datos PostgreSQL 19 diferentes: una para administradores y redactores y otra para suscriptores. La tecnología y la separación ya están decididas por el Anexo I; no tendría sentido compararlas con otra base de datos o con un almacenamiento único.

La decisión pendiente es cómo hacer que las responsabilidades de persistencia del back-end respeten esa separación. El Anexo no indica dónde se almacenan las noticias ni los datos de navegación, de modo que esta decisión no les asigna una base por suposición. Tampoco se añade una tercera base de datos.

## Decision Drivers

* **RD3 — Bases de datos PostgreSQL 19:** el back-end debe incluir una base PostgreSQL 19 para administradores/redactores y otra distinta para suscriptores.

## Considered Options

* **Opción A — Repositorios diferenciados por área de datos.** El back-end utiliza contratos de repositorio distintos para personal y suscriptores; los adaptadores de persistencia se conectan a su base correspondiente.
* **Opción B — Repositorio común con selección dinámica de conexión.** Un único contrato recibe un parámetro que indica el área o tipo de dato y decide a cuál de las dos bases PostgreSQL 19 acceder.

## Decision Outcome

Chosen option: "Opción A — Repositorios diferenciados por área de datos", because hace visible en la arquitectura que los datos de administradores/redactores y de suscriptores tienen accesos separados. La lógica de aplicación utiliza la responsabilidad adecuada sin decidir manualmente a qué conexión debe dirigirse cada operación.

### Consequences

* Good, because cada área cuenta con una responsabilidad de persistencia propia y con acceso a la base prescrita para ella.
* Good, because reduce el riesgo de que una operación con datos de suscriptores utilice la conexión de administradores y redactores.
* Bad, because se necesitan dos configuraciones de conexión y contratos diferenciados aunque ambas bases utilicen PostgreSQL 19.
* Bad, because si hubiera una consulta que necesitara datos de ambas bases, la aplicación tendría que coordinar las dos operaciones.

### Confirmation

El UML debe mostrar dos responsabilidades de acceso diferenciadas, representables por clases como RepositorioPersonal y RepositorioSuscriptores dentro de los paquetes de persistencia, cada una asociada a su correspondiente base PostgreSQL 19. Las dependencias se expresarán entre paquetes, sin dibujar relaciones entre clases. No aparecerá una tercera base ni se decidirá aquí la ubicación de noticias o registros estadísticos, que el Anexo no asigna a una de las dos bases.

## Pros and Cons of the Options

### Opción A — Repositorios diferenciados por área de datos

* Good, because la separación impuesta por el RD3 se reconoce directamente en el modelo de persistencia.
* Good, because cada adaptador queda asociado a una base concreta y evita elegir el destino en cada petición de negocio.
* Bad, because hay que mantener dos conexiones y dos responsabilidades de acceso.
* Bad, because una operación que necesite información de ambas áreas requerirá coordinación en la capa de aplicación, si realmente llega a existir.

### Opción B — Repositorio común con selección dinámica de conexión

* Good, because centraliza utilidades técnicas compartidas para consultar las dos bases PostgreSQL 19.
* Good, because puede ofrecer una interfaz de acceso uniforme para los dos tipos de datos.
* Bad, because un destino indicado incorrectamente puede dirigir información de suscriptores a la conexión de personal o viceversa.
* Bad, because la separación entre ambas responsabilidades es menos visible y puede acabar dispersa en condicionales por tipo de dato.

## Links

* Requisito(s) motivador(es): RD3 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0008-2](./0008-2-acceso-bases-datos.md). Se mantiene la misma solución y se actualizan las referencias a las decisiones vigentes. La anulación del MADR 0008 original consta en 0008-1.
* Decisiones relacionadas: MADR 0001-4, que fija la estructura general. Aunque el RF01 también menciona las bases, su driver corresponde a la elección del estilo; aquí se utiliza RD3 para decidir el modo de acceso.
