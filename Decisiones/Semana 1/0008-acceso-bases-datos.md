# 0008 — Organización del acceso a las dos bases de datos PostgreSQL 19

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I exige **una base de datos PostgreSQL 19 para administradores y redactores y otra para suscriptores**. La tecnología y la separación son obligatorias; no constituyen opciones abiertas. Sí queda por decidir cómo se estructura el acceso desde el back-end, de forma que el código de un área no utilice accidentalmente la conexión de la otra.

El Anexo no determina en qué base se almacenan otros datos, como noticias o registros de uso. Esta decisión **no inventará una tercera base ni atribuirá automáticamente esos datos** a una de las dos existentes.

## Decision Drivers

* **RD3 — Bases de datos PostgreSQL 19:** el back-end debe incluir dos bases de datos diferenciadas, una de administradores/redactores y otra de suscriptores.

## Considered Options

* **0008-1 — Repositorios separados por área de datos.** La lógica de aplicación utiliza interfaces de acceso diferenciadas para personal y suscriptores, implementadas mediante conexiones específicas a cada base de datos.
* **0008-2 — Repositorio genérico con selección dinámica de base de datos.** Una misma abstracción de acceso recibe un tipo de dato o parámetro de destino y decide qué conexión utilizar.

Ambas opciones trabajan exclusivamente con las dos bases PostgreSQL 19 exigidas. La elección afecta al **límite de responsabilidad del acceso**, no al número ni a la tecnología de las bases.

## Decision Outcome

Chosen option: "0008-1 — Repositorios separados por área de datos", because expresa en interfaces y clases la separación obligatoria de las bases y disminuye la posibilidad de que una operación sobre suscriptores utilice la conexión correspondiente a administradores y redactores. Las clases de aplicación no necesitan conocer los detalles de conexión.

### Consequences

* Good, because las dos responsabilidades de persistencia quedan diferenciadas en interfaces y adaptadores, haciendo visible la separación exigida por el Anexo.
* Bad, because habrá dos configuraciones de conexión y cualquier operación que requiera combinar información de ambas bases tendrá que coordinarse desde la lógica de aplicación, si llegara a ser necesaria.
* **Pendiente de Rafa:** dónde se almacenarán los datos no atribuidos explícitamente a personal o suscriptores; no se deduce una tercera base de datos.

### Confirmation

En el diagrama de clases y paquetes deben verse **dos contratos de acceso diferenciados** y sus respectivos adaptadores o repositorios: uno para datos de administradores/redactores y otro para suscriptores. Cada adaptador deberá estar asociado a **una base PostgreSQL 19 distinta**. Ninguna clase de negocio debería necesitar elegir manualmente la conexión a cada base. No se asignarán por suposición noticias o registros estadísticos a una base concreta.

## Pros and Cons of the Options

### 0008-1 — Repositorios separados por área de datos

* Good, because el UML puede distinguir **RepositorioPersonal** y **RepositorioSuscriptores**, cada uno respaldado por su conexión PostgreSQL 19 específica.
* Good, because el código de una operación sobre suscriptores depende de una interfaz tipada para esa responsabilidad, sin seleccionar manualmente el destino en cada llamada.
* Bad, because exige mantener dos configuraciones de conexión y varios contratos de repositorio, incluso si ambos utilizan PostgreSQL 19.
* Bad, because cualquier consulta que deba combinar información de ambas áreas no se resolverá con una unión SQL directa entre repositorios; habrá que coordinarla explícitamente en la aplicación si alguna funcionalidad lo exige.

### 0008-2 — Repositorio genérico con selección dinámica de base

* Good, because centraliza operaciones técnicas comunes de acceso y reduce duplicación de configuración o utilidades PostgreSQL 19.
* Good, because puede ofrecer una interfaz uniforme para consultas sencillas en cualquiera de las dos bases.
* Bad, because una elección errónea del parámetro de destino puede dirigir una operación al área de datos equivocada, pese a que el Anexo exige diferenciarlas.
* Bad, because la interfaz genérica oculta en UML la responsabilidad específica de cada repositorio y puede terminar con condicionales por tipo de entidad o base.

## Links

* Requisito motivador: RD3 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
