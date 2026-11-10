# 0006 — Control de los reintentos de pagos fallidos

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I establece que, cuando falle una petición de pago, **el sistema debe permitir reintentarla hasta tres veces**. No establece que los reintentos sean automáticos, un intervalo entre ellos ni una política de suspensión de la suscripción. Tampoco equipara un rechazo confirmado a una petición cuyo resultado se desconoce por un fallo de comunicación.

La decisión consiste en resolver **quién inicia los nuevos intentos y dónde se controla el límite de tres**, sin convertir una posibilidad de reintento en una obligación de automatizarlos que el texto no formula. Sigue pendiente aclarar con Rafa si espera reintentos automáticos o si basta con que el sistema permita solicitarlos.

## Decision Drivers

* **RF06 — Reintentos de pagos fallidos:** el sistema debe permitir reintentar una petición de pago fallida; el máximo de tres reintentos procede expresamente del Anexo I.

## Considered Options

* **0006-1 — Reintentos a petición del cliente con contador supervisado por el back-end.** Tras un fallo confirmado, el cliente puede solicitar otro intento sobre la misma operación; el servidor verifica cuántos reintentos se han consumido.
* **0006-2 — Reintentos automáticos diferidos gestionados por el back-end.** El sistema programa nuevos intentos de una operación fallida, mantiene el contador y detiene la ejecución al alcanzar el límite.
* **0006-3 — Reintentos automáticos inmediatos desde la petición original.** El servidor vuelve a invocar la pasarela dentro de la misma operación, hasta agotar el número permitido o conseguir un resultado satisfactorio.

Las tres alternativas deben limitar el número de reintentos. La elección afecta al **disparador del nuevo intento**, no a la cantidad permitida.

## Decision Outcome

Chosen option: "0006-1 — Reintentos a petición del cliente con contador supervisado por el back-end", because constituye **la propuesta provisional** que satisface la lectura literal de «permitir reintentar» sin asumir un planificador o avisos automáticos no exigidos expresamente. El control del máximo permanece en el back-end, no en la interfaz. **No se considera cerrada esta elección hasta preguntar a Rafa si los reintentos deben ejecutarse automáticamente**. La alternativa 0006-2 seguiría siendo candidata si ese fuera el alcance esperado.

Se interpretan provisionalmente **tres reintentos adicionales al intento inicial**, que es la lectura natural de «reintentar hasta tres veces». Se confirmará con Rafa si emplea otro criterio de conteo.

### Consequences

* Good, because el servidor puede respetar el máximo de tres reintentos sin introducir un proceso automático de reintento que el Anexo no exige.
* Bad, because un pago rechazado no se volverá a intentar salvo que el cliente solicite una nueva petición; además, el contador debe protegerse frente a peticiones simultáneas.
* Bad, because antes de reintentar un pago con resultado desconocido debe aclararse su estado para evitar un doble cobro; no se interpreta automáticamente como fallo confirmado.
* **Pendiente de Rafa:** confirmar si «permitir reintentar» exige ejecución automática o permite reintentos iniciados por el cliente; hasta entonces esta elección continúa en estado `proposed`.

### Confirmation

El UML deberá representar una operación de pago con un **contador de reintentos** y una responsabilidad de **validar y registrar** cada nuevo intento. Debe apreciarse que el control del límite de tres está en el back-end, independientemente de la petición del cliente. **Si se confirma la alternativa 0006-1**, no debe aparecer un planificador de reintentos automáticos. Si Rafa exige automatización, será necesario revisar la decisión antes de reflejarla como definitiva en UML. La responsabilidad de pago también debe distinguir un fallo confirmado de un resultado desconocido, para evitar duplicaciones al reintentar.

## Pros and Cons of the Options

### 0006-1 — Reintentos a petición del cliente y contador del servidor

* Good, because permite que el cliente solicite un nuevo intento solo cuando existe un fallo confirmado, sin mantener procesos programados adicionales.
* Good, because el back-end conserva la autoridad sobre el contador: una petición que exceda el máximo se rechaza aunque el cliente repita la solicitud.
* Bad, because el cobro no se volverá a intentar si el cliente no realiza otra petición; el Anexo permite reintentar, pero no garantiza que el cliente lo haga.
* Bad, because requiere asociar las peticiones sucesivas a una misma operación de cobro y proteger las actualizaciones del contador frente a peticiones simultáneas.

### 0006-2 — Reintentos automáticos diferidos

* Good, because un fallo temporal de la pasarela podría resolverse antes del siguiente intento, sin intervención del cliente.
* Good, because el contador y la programación quedan centralizados en el back-end.
* Bad, because añade planificación, estados pendientes y elección de intervalos que el Anexo no establece.
* Bad, because obliga a decidir qué sucede mientras un pago está pendiente de nuevos intentos, lo que abre reglas de negocio adicionales que deben aclararse con Rafa.

### 0006-3 — Reintentos automáticos inmediatos

* Good, because mantiene el flujo concentrado en una operación y evita un componente planificador independiente.
* Good, because puede recuperar un fallo transitorio breve sin que el cliente tenga que volver a solicitar el cobro.
* Bad, because agota rápidamente todos los reintentos cuando el fallo no se resuelve de forma inmediata, por ejemplo un rechazo persistente.
* Bad, because alarga el tiempo de respuesta del cobro inicial y puede producir esperas incompatibles con la petición HTTP original.

## Links

* Requisito motivador: RF06 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Cuestión pendiente para Rafa (martes 10/11): ¿los reintentos pueden ser solicitados por el cliente o deben lanzarse automáticamente?, ¿el máximo de tres son reintentos adicionales al primer intento?
