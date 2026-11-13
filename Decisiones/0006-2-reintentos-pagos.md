# 0006-2 — Control de los reintentos de pagos fallidos

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

Cuando una petición de pago falla, el sistema debe permitir reintentarla un máximo de tres veces. El RF07 incorpora expresamente ese límite. Ni el Anexo I ni el requisito indican que los reintentos deban ser automáticos, qué intervalo debe haber entre ellos o qué ocurre con una suscripción cuyo pago no se consigue completar.

La decisión consiste en determinar cómo se inicia un nuevo intento y dónde se controla el límite. También es necesario distinguir un fallo confirmado de una petición cuyo resultado se desconoce: volver a cobrar sin verificar este último caso podría duplicar una operación. El texto habla de «reintentos», por lo que se considera el intento inicial separado de los tres posibles reintentos; si el profesor emplea otra interpretación, deberá ajustarse el contador sin cambiar la responsabilidad arquitectónica.

## Decision Drivers

* **RF07 — Reintentos de pagos fallidos:** el sistema debe permitir hasta tres reintentos cuando una petición de pago haya fallado.

## Considered Options

* **Opción A — Reintentos solicitados por el cliente y controlados por el back-end.** Tras un fallo confirmado, se puede solicitar un nuevo cobro sobre la operación original; el servidor registra y limita los reintentos.
* **Opción B — Reintentos automáticos diferidos.** El servidor programa los nuevos intentos después de un fallo y detiene el proceso cuando tiene éxito o se alcanza el límite.
* **Opción C — Reintentos automáticos inmediatos.** Durante la misma operación se realizan nuevos intentos seguidos hasta obtener un resultado o agotar el máximo.

## Decision Outcome

Chosen option: "Opción A — Reintentos solicitados por el cliente y controlados por el back-end", because satisface la obligación de permitir hasta tres nuevas solicitudes sin dar por exigido un mecanismo automático que no aparece en el Anexo. El servidor, y no el front-end, mantiene la cuenta de reintentos y evita superar el límite aunque lleguen varias peticiones.

### Consequences

* Good, because el número máximo de reintentos depende del estado de la operación en el back-end y no de la voluntad del navegador.
* Bad, because si el cliente no solicita un nuevo intento, el cobro no se vuelve a tramitar por sí solo.
* Bad, because es necesario controlar peticiones repetidas o simultáneas y no reintentar un resultado desconocido como si fuese un fallo confirmado.

### Confirmation

El UML deberá localizar en el back-end una responsabilidad para comprobar y actualizar el número de reintentos de una operación de pago. El límite será de tres reintentos posteriores al primero según la interpretación adoptada, y una operación con resultado no confirmado no se tratará sin más como fallida. Si el profesor aclara que los reintentos deben ser automáticos o que el máximo se cuenta de otra manera, se revisará la solución y se creará una nueva versión de la decisión. No se introducirán planificadores automáticos en el diagrama si se conserva la opción A.

## Pros and Cons of the Options

### Opción A — Reintentos solicitados por el cliente y controlados por el back-end

* Good, because permite nuevos intentos solo cuando consta un fallo previo y aún quedan reintentos disponibles.
* Good, because el contador centralizado impide que repetir una petición HTTP elimine el límite establecido.
* Bad, because la recuperación de un cobro fallido depende de que el cliente vuelva a solicitarlo.
* Bad, because necesita identificar la misma operación entre peticiones y evitar incrementos inconsistentes del contador.

### Opción B — Reintentos automáticos diferidos

* Good, because un error transitorio de la pasarela puede superarse sin intervención del lector.
* Good, because la programación y el máximo de reintentos se controlan en el back-end.
* Bad, because añade un planificador y reglas sobre cuándo volver a intentar que el Anexo no proporciona.
* Bad, because obliga a decidir cómo se presenta una operación pendiente de nuevos intentos, abriendo reglas adicionales para pagos no completados.

### Opción C — Reintentos automáticos inmediatos

* Good, because evita componentes de planificación y mantiene la operación concentrada en una solicitud.
* Good, because permite recuperar un fallo transitorio muy breve sin una nueva acción del cliente.
* Bad, because puede consumir los tres reintentos de inmediato ante un rechazo que no cambiará en segundos.
* Bad, because puede prolongar en exceso la respuesta HTTP y deja poco margen para verificar estados inciertos.

## Links

* Requisito(s) motivador(es): RF07 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0006](./0006-reintentos-pagos.md). La anulación anterior queda registrada como 0006-1.
* Decisiones relacionadas: MADR 0005-2, que delimita la integración con Stripe y no repite el RF07 como driver.
