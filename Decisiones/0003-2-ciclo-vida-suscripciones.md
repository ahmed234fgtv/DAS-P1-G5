# 0003-2 — Gestión anónima de las suscripciones y sus bajas

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

El Anexo I exige que un lector pueda suscribirse a noticias y darse de baja de forma anónima. Las categorías posibles son política, economía, sociedad, nacionales e internacionales. Los requisitos RF03 y RF04 mantienen estas condiciones, pero no indican cómo puede alguien gestionar posteriormente una suscripción sin identificarse, ni si debe desaparecer el registro cuando solicite la baja.

El problema de diseño es decidir cómo distinguir una suscripción concreta, autorizar su baja de manera anónima y representar el fin de su vigencia. No se fija si una noticia puede tener una o varias categorías, ni se convierte la suscripción a noticias en una contratación de pago por el mero hecho de utilizar la misma palabra. Tampoco se supone que exista un envío automático de noticias: el canal permite gestionar altas y bajas desde el front-end mediante las peticiones previstas para el sistema.

## Decision Drivers

* **RF03 — Suscripción a noticias:** el lector puede suscribirse anónimamente a noticias de las cinco categorías del Anexo.
* **RF04 — Baja de suscripciones a noticias:** el suscriptor puede darse de baja de forma anónima.

## Considered Options

* **Opción A — Clave de gestión por suscripción y baja lógica.** Cada alta genera una referencia opaca que permite solicitar la baja; la suscripción pasa a inactiva y conserva un registro mínimo de su estado.
* **Opción B — Clave de gestión por suscripción y eliminación del registro.** La referencia opaca permite solicitar la baja, pero esta elimina la relación de suscripción almacenada.
* **Opción C — Suscripción asociada a la sesión y baja lógica.** La sesión anónima desde la que se creó la suscripción se utiliza para localizarla y darla de baja, conservando los registros inactivos.

## Decision Outcome

Chosen option: "Opción A — Clave de gestión por suscripción y baja lógica", because permite dar de baja una suscripción sin exigir una identidad personal y sin depender de que continúe abierta la misma sesión del navegador. La marca de baja separa las suscripciones activas de las canceladas; no se adopta por ello una obligación de conservar los datos indefinidamente.

### Consequences

* Good, because la baja puede solicitarse con la referencia de la suscripción incluso después de finalizar la sesión de lectura.
* Good, because queda representado de forma clara qué suscripciones están vigentes y cuáles han sido dadas de baja.
* Bad, because si se pierde la referencia, el lector puede no disponer de un mecanismo para acreditar qué suscripción desea dar de baja.
* Bad, because las consultas deben filtrar los registros inactivos y debe decidirse qué información se conserva tras la baja.

### Confirmation

El UML debe incluir las responsabilidades de gestionar altas y bajas anónimas dentro del módulo de suscripciones y una representación de la suscripción con su estado de vigencia o fecha de baja. También deben quedar recogidas las cinco categorías de noticias del Anexo, sin imponer una cardinalidad no indicada. El canal de suscripciones será accesible desde el front-end a través de la entrada HTTP/REST. No se añadirán mecanismos de distribución automática de noticias, cuentas obligatorias ni estados por impago. Las relaciones arquitectónicas se expresarán entre paquetes.

## Pros and Cons of the Options

### Opción A — Clave de gestión por suscripción y baja lógica

* Good, because la referencia permite localizar la suscripción sin mantener abierto el navegador donde se creó.
* Good, because el estado inactivo evita que una suscripción cancelada aparezca como vigente y permite conservar la referencia de una baja.
* Bad, because la referencia tiene que conservarse de forma segura, pues quien la posea podría solicitar la cancelación.
* Bad, because es necesario distinguir sistemáticamente registros activos e inactivos y limitar la conservación de información innecesaria.

### Opción B — Clave de gestión por suscripción y eliminación del registro

* Good, because las consultas operativas solo encuentran suscripciones vigentes, sin filtrar estados de baja.
* Good, because no conserva la relación cancelada después de ejecutar la eliminación.
* Bad, because desaparece la referencia al alta y a la baja, lo que dificulta comprobar una cancelación pasada si fuera necesario.
* Bad, because mantiene el mismo problema de custodia de la referencia de gestión mientras la suscripción está activa.

### Opción C — Suscripción asociada a la sesión y baja lógica

* Good, because permite localizar las suscripciones creadas durante la sesión sin emitir una referencia diferente por cada alta.
* Good, because distingue activas e inactivas sin eliminar los registros.
* Bad, because una sesión puede caducar antes de que el lector desee cancelar una suscripción, impidiendo la baja con ese único mecanismo.
* Bad, because acopla una relación potencialmente duradera a la identificación temporal de clientes tratada en el MADR 0002-2.

## Links

* Requisito(s) motivador(es): RF03, RF04 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0003](./0003-ciclo-vida-suscripciones.md). La anulación anterior queda registrada como 0003-1.
* Decisiones relacionadas: MADR 0002-2 (diferenciación de clientes) y MADR 0007-2 (estadísticas); estas referencias no añaden otros drivers.
