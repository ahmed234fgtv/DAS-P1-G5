# 0007 — Tratamiento de los reintentos de pagos fallidos

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF06 exige permitir reintentar una petición de pago cuando esta haya fallado. El Anexo fija un máximo de tres reintentos, pero no dice cuándo deben producirse, quién los inicia ni qué ocurre con la suscripción mientras se reintenta.

El problema es decidir dónde vive el estado del reintento y en qué momento se ejecuta. Un reintento inmediato bloquea la operación de cobro y no da tiempo a que el motivo del fallo desaparezca; un reintento sin contador persistido no puede respetar el límite de tres; y un reintento delegado a la pasarela deja el control fuera del sistema.

## Decision Drivers

* **RF06 — Reintentos de pagos fallidos:** el sistema debe permitir reintentar una petición de pago cuando esta haya fallado.

## Considered Options

* 0007-1 — Reintento inmediato en el mismo hilo de la petición de cobro.
* 0007-2 — Reintento diferido con tarea programada y contador persistido por pago.
* 0007-3 — Delegar en los reintentos automáticos de Stripe.
* 0007-4 — Reintento manual iniciado por el suscriptor.

## Decision Outcome

Chosen option: **0007-2 — Reintento diferido con tarea programada y contador persistido**, because es la única opción que respeta el límite de tres reintentos de forma verificable y permite que el motivo del fallo desaparezca entre intentos.

### Pros and Cons of the Options

#### 0007-2 — Reintento diferido con tarea programada y contador persistido

* Good, because el contador de intentos se guarda junto al pago, de modo que el sistema puede demostrar que no ha superado el máximo de tres reintentos aunque se reinicie el proceso.
* Good, because separa el reintento de la petición original: el suscriptor recibe respuesta inmediata del primer intento y el sistema sigue trabajando en segundo plano.
* Good, because deja un registro por intento con su fecha y resultado, que es lo que permite explicar qué ocurrió con un cobro fallido.
* Bad, because obliga a mantener una tarea programada que revise los pagos pendientes, lo que añade un componente con su propia configuración de frecuencia.
* Bad, because la suscripción queda en un estado intermedio mientras se reintenta, y hay que decidir si durante ese periodo el suscriptor sigue recibiendo noticias.

#### 0007-1 — Reintento inmediato en el mismo hilo

* Good, because no necesita planificación ni estado persistido: el bucle de reintentos está en el propio método de cobro.
* Bad, because los tres reintentos se ejecutan en segundos, y los fallos por fondos insuficientes o por rechazo del emisor no se resuelven en ese plazo, de modo que se agotan los intentos sin ninguna posibilidad real de éxito.
* Bad, because el suscriptor espera la respuesta de la operación de alta o de renovación durante todos los intentos, lo que degrada la experiencia y puede agotar el tiempo de espera del cliente.

#### 0007-3 — Delegar en los reintentos automáticos de Stripe

* Good, because Stripe ya implementa una política de reintentos probada y no habría que programar nada.
* Bad, because el contador de intentos queda en la pasarela, de modo que el sistema no puede verificar por sí mismo que se respeta el límite de tres.
* Bad, because el comportamiento depende de una configuración del panel de Stripe y no del código, lo que hace que la decisión no sea verificable en el repositorio ni en el diagrama.

#### 0007-4 — Reintento manual iniciado por el suscriptor

* Good, because el suscriptor decide cuándo volver a intentarlo, lo que evita reintentos inútiles.
* Bad, because el sistema no garantiza que se lleguen a hacer los tres reintentos, y el requisito habla de que el sistema permita reintentar, no de que el usuario lo solicite.
* Bad, because exige un mecanismo de notificación al suscriptor y una interfaz para reintentar, que son decisiones que ningún requisito de la tabla respalda.

## Consequences

* Good, because el límite de tres reintentos es verificable consultando el contador del pago.
* Good, because cada intento queda registrado con fecha y resultado, lo que permite auditar los cobros fallidos.
* Bad, because hay que definir el intervalo entre reintentos y documentarlo, ya que el Anexo no lo fija.
* Bad, because hay que decidir qué ocurre con la suscripción mientras se reintenta, y esa decisión no está cubierta por ningún requisito actual.

## Links

* Requisitos: RF06.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0006 (integración de pagos), 0005 (ciclos de facturación).

---

