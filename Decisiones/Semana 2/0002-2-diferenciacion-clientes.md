# 0002-2 — Mecanismo de diferenciación de clientes

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

El sistema debe distinguir a cada cliente que acceda al periódico digital. El Anexo I no aclara si «cliente» se refiere a una persona, un dispositivo, un navegador o una sesión. Tampoco exige registrar lectores con datos personales. La presentación responsiva para PC y tablet permite adaptar la interfaz, pero no permite distinguir individualmente a quienes hacen peticiones.

Para dar una solución representable en el diseño, es necesario decidir cómo reconocer varias peticiones procedentes del mismo cliente sin imponer un registro que no aparece en los requisitos. La decisión no pretende garantizar la identidad real de una persona.

## Decision Drivers

* **RF02 — Diferenciación de clientes:** el sistema debe ser capaz de distinguir a cada cliente que acceda al periódico digital.

## Considered Options

* **Opción A — Identificador de sesión emitido por el back-end.** El servidor entrega un identificador opaco que el navegador devuelve en sus peticiones posteriores mientras la sesión siga vigente.
* **Opción B — Identificador persistente generado en el navegador.** El navegador crea un identificador propio, lo conserva localmente y lo envía con sus peticiones para que el back-end distinga su origen.

## Decision Outcome

Chosen option: "Opción A — Identificador de sesión emitido por el back-end", because el servidor controla la emisión y la caducidad de los identificadores utilizados para diferenciar peticiones y no obliga a crear una cuenta de lector. Es una respuesta concreta al RF02, aunque no equivale a identificar personas distintas de manera permanente.

### Consequences

* Good, because se pueden asociar varias peticiones a una misma sesión sin pedir nombre, correo ni contraseña.
* Bad, because una misma persona puede tener sesiones diferentes si cambia de navegador o dispositivo o si caduca el identificador.
* Bad, because debe definirse cómo se transmite y protege el identificador para impedir que una sesión sea suplantada.

### Confirmation

El UML debe situar la responsabilidad de crear y comprobar identificadores de sesión dentro de la entrada o de un componente de apoyo del back-end. El front-end se comunica con esa entrada mediante HTTP/REST. Se podrá incluir una clase representativa de sesión, sin relaciones entre clases. No se añadirá un registro de usuarios obligatorio ni se afirmará que una sesión corresponde a una persona única. La duración exacta de una sesión queda abierta, ya que el Anexo no la fija.

## Pros and Cons of the Options

### Opción A — Identificador de sesión emitido por el back-end

* Good, because el servidor puede rechazar identificadores no emitidos por él y gestionar cuándo dejan de ser válidos.
* Good, because las peticiones de una misma sesión se pueden diferenciar sin ampliar el requisito con credenciales personales.
* Bad, because cerrar el navegador, borrar sus datos o usar otra tablet puede generar otra sesión para el mismo lector.
* Bad, because exige una política de caducidad y un tratamiento cuidadoso del identificador, aunque este no contenga nombres.

### Opción B — Identificador persistente generado en el navegador

* Good, because permite reconocer de nuevo al mismo navegador después de una visita sin pedir un alta.
* Good, because el identificador puede incluirse desde la primera petición, sin que el servidor tenga que emitirlo antes.
* Bad, because el navegador puede modificarlo o regenerarlo, de modo que el servidor no puede garantizar su unicidad.
* Bad, because su persistencia prolongada afecta al tratamiento de la actividad del lector y tampoco resuelve el uso de varios dispositivos por una misma persona.

## Links

* Requisito(s) motivador(es): RF02 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0002](./0002-diferenciacion-clientes.md). La anulación anterior queda registrada como 0002-1.
* Decisiones relacionadas: MADR 0007-2; los datos de sesión pueden ayudar a agrupar interacciones, pero no sustituyen las mediciones necesarias para las estadísticas.
