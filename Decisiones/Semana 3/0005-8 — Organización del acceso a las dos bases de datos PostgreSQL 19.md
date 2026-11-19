# 0005-8 — Organización del acceso a las dos bases de datos PostgreSQL 19

* Status: proposed
* Date: 2026-11-19

## Context and Problem Statement

El RD3 establece que el back-end debe contar con dos bases de datos PostgreSQL 19 diferentes: una para administradores y redactores y otra para suscriptores. La tecnología y la separación ya están decididas por el Anexo I; no tendría sentido compararlas con otra base de datos o con un almacenamiento único.

La decisión pendiente es cómo hacer que las responsabilidades de persistencia del back-end respeten esa separación. El Anexo no indica dónde se almacenan las noticias ni los datos de navegación, de modo que esta decisión no les asigna una base por suposición. Tampoco se añade una tercera base de datos.

## Decision Drivers

* **RD3 — Bases de datos PostgreSQL 19:** el back-end debe incluir una base PostgreSQL 19 para administradores/redactores y otra distinta para suscriptores.

## Considered Options

* **Opción A — Acceso diferenciado por ámbito de datos.** El acceso a los datos de administradores y redactores se gestiona por separado del acceso a los datos de suscriptores. Cada ámbito tiene asignada su base PostgreSQL 19.
* **Opción B — Acceso común con selección de la base de datos.** Los dos ámbitos comparten el acceso a datos. Para cada operación se selecciona la base PostgreSQL 19 correspondiente según el ámbito de los datos solicitados.

## Decision Outcome

Chosen option: "Opción A — Acceso diferenciado por ámbito de datos", because permite comprobar de forma directa qué base corresponde a cada ámbito y reduce la posibilidad de seleccionar un destino incorrecto en una operación concreta.

### Consequences

* Good, because se puede verificar por separado el acceso a los datos de administradores y redactores y el acceso a los datos de suscriptores.
* Bad, because cualquier regla de acceso que afecte a ambos ámbitos debe mantenerse coherente en los dos.

### Confirmation

Comprobaremos que una consulta de datos de administradores o redactores accede a su base PostgreSQL 19 y que una consulta de suscriptores accede a la otra base PostgreSQL 19. Comprobaremos también que el destino de acceso queda asignado de antemano para cada ámbito.

## Pros and Cons of the Options

### Opción A — Acceso diferenciado por ámbito de datos

* Good, because un cambio que afecte solo al acceso a los datos de suscriptores se puede revisar dentro de ese ámbito.
* Bad, because los cambios comunes a los dos ámbitos requieren revisar ambos accesos.

### Opción B — Acceso común con selección de la base de datos

* Good, because el criterio para determinar el destino de cada acceso se mantiene en común para los dos ámbitos.
* Bad, because un error en ese criterio puede dirigir una operación a la base equivocada y afectar a cualquiera de los dos ámbitos.

## Links

* Requisito(s) motivador(es): RD3 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye al [MADR 0005-6](<./0005-6 — Organización del acceso a las dos bases de datos PostgreSQL 19.md>), según consta en la [anulación 0005-7](<./0005-7 Anulamos Organización del acceso a las dos bases de datos PostgreSQL 19.md>).
* Decisión relacionada: [MADR 0001-8](<./0001-8 — Selección del estilo arquitectónico del periódico digital.md>), que fija la estructura general. Aunque el RF01 también menciona las bases, su driver corresponde a la elección del estilo. Aquí se utiliza RD3 para decidir el modo de acceso.
