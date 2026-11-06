# 0003 — Gestión anónima del ciclo de vida de las suscripciones a noticias

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I exige que los lectores puedan suscribirse a diferentes noticias y darse de baja **de forma anónima**. Cada noticia pertenece a una de las cinco categorías que el Anexo fija expresamente: política, economía, sociedad, nacionales o internacionales; esta decisión no añade ni modifica esas categorías, aunque deja pendiente si la suscripción opera sobre la noticia individual o sobre la categoría. No concreta cómo acredita una persona anónima que puede gestionar una suscripción determinada. Tampoco indica qué debe ocurrir con el registro de una suscripción después de su baja: podría eliminarse o conservarse como inactivo. Estas opciones producen consecuencias diferentes para las consultas de suscripciones vigentes y para la disponibilidad de información histórica.

El problema es determinar **cómo representar el alta y la baja de una suscripción, cómo autorizar su gestión sin identificación nominal y cómo reflejar la baja en los datos**. Las soluciones comparadas resuelven conjuntamente estas responsabilidades del ciclo de vida. El Anexo no exige notificaciones ni envíos automáticos de noticias; esta decisión no los introduce. El canal de suscripciones del Anexo se representa como un servicio al que accede el front-end mediante HTTP/REST para gestionar las altas y bajas anónimas, sin incorporar mecanismos de entrega automática de noticias que el Anexo no exige expresamente. Tampoco presupone que suscribirse a una noticia equivalga necesariamente a contratar una modalidad de pago.

## Decision Drivers

* **RF02 — Suscripción a noticias:** el sistema debe permitir a los lectores suscribirse a diferentes noticias.
* **RF03 — Baja de suscripciones a noticias:** el sistema debe permitir a los suscriptores darse de baja de sus suscripciones a noticias.

## Considered Options

* **0003-1 — Clave opaca por suscripción y baja lógica.** Cada alta crea una suscripción con una referencia secreta de gestión; la baja deja de considerarla vigente y conserva su registro con una marca de baja.
* **0003-2 — Clave opaca por suscripción y borrado físico.** El cliente acredita la gestión mediante una referencia secreta, pero la baja elimina el registro de la relación con la noticia.
* **0003-3 — Suscripción vinculada a la sesión y baja lógica.** Las altas y bajas se gestionan usando la sesión anónima del navegador; las suscripciones dadas de baja se conservan como inactivas.

Las tres alternativas permiten alta y baja sin pedir una identidad nominal. Las dos primeras distinguen el tratamiento del registro al cancelar; la primera y la tercera comparan cómo se acredita el derecho de gestión. No se proponen estados adicionales como suspensión por impago, porque el Anexo no los define.

## Decision Outcome

Chosen option: "0003-1 — Clave opaca por suscripción y baja lógica", because permite dar de baja una suscripción concreta sin vincular su vigencia a una sesión de navegador y evita perder inmediatamente la referencia y la fecha de una baja. Mantener el registro como inactivo hace posible consultar el historial de altas y bajas si resulta necesario para concretar las estadísticas, **pero no se afirma que el borrado lógico sea imprescindible para calcular RF10**: la conversión también podría medirse mediante un registro mínimo del evento de alta. La alternativa seleccionada se propone por su claridad en el ciclo de vida y la posibilidad de conservar ese dato, no porque el Anexo imponga retención indefinida.

### Consequences

* Good, because una baja deja de considerar vigente la suscripción sin necesitar reconstruir qué noticia estaba asociada a la referencia de gestión.
* Good, because el sistema conserva una referencia al alta y a la baja que podría servir para contrastar métricas históricas si se acuerda su definición.
* Bad, because las consultas de suscripciones vigentes deben excluir sistemáticamente los registros dados de baja; omitir ese filtro produciría resultados incorrectos.
* Bad, because conservar información tras la baja requiere justificar qué campos permanecen y durante cuánto tiempo; **no se ha decidido una conservación ilimitada**.
* **Pendiente de Rafa:** si la suscripción se refiere a una noticia o a una categoría y cómo se relaciona, si procede, con las modalidades de pago. Tampoco se presupone una regla de retención temporal impuesta por el profesor.

### Confirmation

El UML deberá reflejar una **SuscripcionNoticia** identificable, relacionada con la noticia a la que corresponde, y las operaciones de **alta y baja** en un componente responsable de gestionarla. La noticia dispondrá de su **categoría** entre las cinco prescritas por el Anexo (política, economía, sociedad, nacionales, internacionales), como dato de dominio sin decisión arquitectónica adicional. La suscripción tendrá un indicador de vigencia o una fecha de baja que permita distinguir suscripciones activas e inactivas. Debe representarse la comprobación de la **referencia opaca de gestión** para solicitar la baja, sin exigir una cuenta nominal. La arquitectura no añadirá mecanismos de entrega de noticias, suspensión por impago ni un historial independiente que esta decisión no selecciona. Las consultas del servicio deberán distinguir suscripciones vigentes de las dadas de baja. El **servicio del canal de suscripciones** y su relación con el front-end mediante HTTP/REST deberán aparecer en el UML.

## Pros and Cons of the Options

### 0003-1 — Clave opaca por suscripción y baja lógica

* Good, because la referencia permite localizar una suscripción concreta incluso si caduca la sesión anónima con la que se dio de alta.
* Good, because una marca de baja conserva la relación original con la noticia y el momento en que dejó de estar vigente, sin crear un almacén de eventos separado.
* Bad, because el cliente debe conservar una clave que actúa como credencial de gestión; su pérdida dificulta la baja y su divulgación puede permitir que otra persona la solicite.
* Bad, because exige aplicar un filtro de vigencia a todas las consultas operativas y mantener datos de suscripciones canceladas que quizá no resulten necesarios.

### 0003-2 — Clave opaca por suscripción y borrado físico

* Good, because tras una baja no quedan registros inactivos que puedan aparecer accidentalmente en las consultas de suscripciones vigentes.
* Good, because se minimiza la conservación de la relación cancelada, en un contexto donde el Anexo exige anonimato.
* Bad, because se pierde directamente la referencia a la suscripción y su baja; si se necesitase contar altas históricas para alguna estadística, habría que disponer de un registro mínimo independiente.
* Bad, because la clave opaca sigue siendo necesaria durante la vigencia y tiene el mismo riesgo de pérdida o divulgación que en la primera opción.

### 0003-3 — Suscripción vinculada a la sesión y baja lógica

* Good, because permite localizar las suscripciones de la sesión actual sin proporcionar una referencia distinta para cada una.
* Good, because conserva las altas y bajas en el mismo modelo de suscripción sin un historial adicional.
* Bad, because la suscripción puede seguir vigente después de caducar la sesión utilizada para crearla; sin otro mecanismo, el cliente ya no podría acreditar una baja posterior.
* Bad, because acopla la gestión de una suscripción potencialmente duradera al mecanismo de diferenciación temporal de clientes previsto en el MADR 0002.

## Links

* Requisitos motivadores: RF02 y RF03 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Decisiones relacionadas: MADR 0002 (diferenciación de clientes) y MADR 0007 (estadísticas de uso). Estas referencias cruzadas no reutilizan sus requisitos como *Decision Drivers*.
