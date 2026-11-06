# 0009 — Organización del acceso a las dos bases de datos

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RD3 exige que la persistencia se implante sobre PostgreSQL 19 y que el back-end disponga de dos bases de datos independientes: una para administradores y redactores, y otra para los suscriptores.

El problema es decidir cómo accede la capa de negocio a dos orígenes de datos distintos sin que las clases de dominio sepan cuál están usando. Si el acceso se dispersa en llamadas directas, cada servicio tendrá que decidir a qué base conectarse, y esa decisión quedará repetida en cada operación que implique entidades de las dos bases.

## Decision Drivers

* **RD3 — Gestor de base de datos PostgreSQL 19:** la persistencia del sistema debe implantarse sobre PostgreSQL 19, con dos bases de datos independientes, una para administradores y redactores y otra para los suscriptores.

## Considered Options

* 0009-1 — Un repositorio por agregado, con dos unidades de persistencia separadas (una por base de datos).
* 0009-2 — Un único repositorio con dos esquemas dentro de la misma instancia.
* 0009-3 — Acceso directo con SQL desde los servicios de negocio.
* 0009-4 — Un DAO genérico único que enruta cada entidad a la base que le corresponde.

## Decision Outcome

Chosen option: **0009-1 — Un repositorio por agregado con dos unidades de persistencia**, because respeta la separación de bases exigida por RD3 y mantiene el dominio ajeno a la existencia de dos orígenes.

### Pros and Cons of the Options

#### 0009-1 — Un repositorio por agregado con dos unidades de persistencia

* Good, because cada agregado del dominio tiene su repositorio, y la clase de negocio pide "guarda esta noticia" sin saber si se persiste en la base de redactores o en la de suscriptores.
* Good, because la separación de bases de RD3 queda reflejada en dos unidades de persistencia explícitas, de modo que el diagrama de arquitectura muestra la frontera y no la oculta.
* Good, because permite que las noticias y las suscripciones evolucionen con esquemas distintos sin que un cambio en una base obligue a revisar la otra.
* Bad, because una operación que afecte a entidades de las dos bases no puede resolverse con una única transacción, y hay que decidir qué hacer si la segunda escritura falla.
* Bad, because hay que mantener dos configuraciones de conexión y dos conjuntos de migraciones, lo que duplica el trabajo de puesta en marcha.

#### 0009-2 — Un único repositorio con dos esquemas

* Good, because una sola conexión y una sola configuración simplifican el despliegue y permiten transacciones que cruzan ambos esquemas.
* Bad, because incumple RD3, que exige dos bases de datos independientes y no dos esquemas dentro de la misma base.
* Bad, because la separación queda solo nominal: cualquier consulta puede cruzar los dos esquemas, de modo que la frontera entre administradores y suscriptores no está garantizada por la arquitectura.

#### 0009-3 — Acceso directo con SQL desde los servicios de negocio

* Good, because elimina las capas intermedias y hace visible de inmediato qué consulta se ejecuta.
* Bad, because mezcla el lenguaje de persistencia con las reglas de negocio, de modo que un cambio de esquema obliga a modificar los servicios de suscripciones y de estadísticas.
* Bad, because cada servicio tendría que decidir a qué base conectarse, repitiendo esa decisión en cada operación y haciendo imposible garantizar que se respeta la separación de RD3.

#### 0009-4 — Un DAO genérico que enruta por tipo de entidad

* Good, because centraliza en un único punto la decisión de a qué base va cada entidad.
* Bad, because un DAO genérico acaba exponiendo operaciones comunes a entidades con necesidades distintas, y las que no encajan se resuelven con casos especiales.
* Bad, because el enrutado por tipo de entidad es una regla implícita: si se añade un agregado nuevo y se olvida registrarlo, se persiste en la base equivocada sin ningún error visible.

## Consequences

* Good, because el dominio no conoce la existencia de dos bases y las decisiones de negocio no dependen del esquema de persistencia.
* Good, because la separación exigida por RD3 es visible en el diagrama de arquitectura como dos unidades de persistencia.
* Bad, because hay que definir qué ocurre cuando una operación necesita escribir en las dos bases y la segunda escritura falla.
* Bad, because el despliegue requiere dos configuraciones de conexión y dos juegos de migraciones que mantener sincronizados.

## Links

* Requisitos: RD3.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0001 (estilo arquitectónico), 0005 (ciclos de facturación).
