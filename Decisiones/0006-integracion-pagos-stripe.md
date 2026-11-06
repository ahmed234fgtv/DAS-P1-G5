# 0006 — Integración de los pagos online mediante Stripe

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF05 exige gestionar los pagos online de las suscripciones, y RD2 impone que el cobro se tramite a través de la pasarela STRIPE.

El problema es decidir cómo se relaciona el dominio del periódico con la API de Stripe. Stripe impone sus propios objetos, sus propios identificadores y su propio modelo de errores, y el sistema necesita además registrar el resultado de cada pago. Si la lógica de suscripciones y facturación habla directamente con el SDK, cualquier cambio en la versión de la API o en el modelo de respuesta se propaga por todo el dominio.

## Decision Drivers

* **RF05 — Gestión de pagos online:** el sistema debe gestionar los pagos online de las suscripciones.
* **RD2 — Pasarela de pago STRIPE:** el cobro de las suscripciones debe tramitarse a través de la pasarela de pago STRIPE.

## Considered Options

* 0006-1 — Adapter: interfaz propia de pasarela de pago con un adaptador que traduce a Stripe.
* 0006-2 — Llamada directa al SDK de Stripe desde el servicio de suscripciones.
* 0006-3 — Facade sobre el SDK que agrupa las operaciones habituales.
* 0006-4 — Servicio intermediario propio que envuelve la pasarela por red.

## Decision Outcome

Chosen option: **0006-1 — Adapter**, because aísla el dominio de los tipos y errores de Stripe y permite que el resto del sistema trate el pago como una operación propia.

### Pros and Cons of the Options

#### 0006-1 — Adapter

* Good, because el dominio trabaja con una interfaz de pasarela propia (cobrar, consultar estado) y no con los objetos de Stripe, de modo que el registro del resultado del pago se hace sobre un modelo del sistema y no sobre la respuesta del SDK.
* Good, because si Stripe cambia su API o se añade otra pasarela, el cambio queda confinado al adaptador, sin tocar suscripciones ni facturación.
* Good, because los errores de Stripe se traducen a un conjunto propio de resultados, lo que permite tratar igual un rechazo de tarjeta y un fallo de red sin que el dominio conozca los códigos de la pasarela.
* Bad, because hay que mantener la traducción entre el modelo propio y el de Stripe, y esa traducción es un punto donde se pueden perder datos que la pasarela devuelve.
* Bad, because añade una interfaz y una clase al diagrama para una única pasarela, ya que RD2 fija Stripe y no se prevé cambiarla.

#### 0006-2 — Llamada directa al SDK

* Good, because es la opción con menos código: no hay traducción ni interfaz intermedia.
* Bad, because los tipos de Stripe aparecen en las clases de dominio, de modo que el diagrama de clases del sistema queda mezclado con el de la pasarela.
* Bad, because el registro del resultado del pago dependería de la forma exacta de la respuesta de Stripe, lo que obliga a revisar ese registro con cada actualización del SDK.

#### 0006-3 — Facade sobre el SDK

* Good, because reduce el número de llamadas que el dominio hace al SDK y agrupa las operaciones habituales en un punto.
* Bad, because una fachada no traduce el modelo: sigue exponiendo los tipos de Stripe hacia el dominio, de modo que no resuelve el acoplamiento que motiva la decisión.
* Bad, because si la fachada crece acaba siendo un adaptador mal nombrado, con la desventaja de que su contrato no está definido explícitamente.

#### 0006-4 — Servicio intermediario propio por red

* Good, because permitiría cambiar de pasarela sin recompilar el back-end.
* Bad, because añade un salto de red y un componente desplegable que ningún requisito del Anexo exige, y complica el diagrama de arquitectura.
* Bad, because el resultado del pago llegaría de forma asíncrona, lo que obliga a decidir cómo se notifica al back-end y cómo se evita procesar dos veces el mismo cobro.

## Consequences

* Good, because el dominio del periódico no depende de Stripe y el diagrama de clases se mantiene legible.
* Good, because el resultado del pago se modela con un tipo propio, lo que facilita registrarlo y consultarlo.
* Bad, because hay que definir el contrato de la interfaz de pasarela antes de modelar el diagrama de clases.
* Bad, because la traducción de errores debe cubrir los casos que la pasarela devuelve, y un caso no contemplado se tratará como fallo genérico.

## Links

* Requisitos: RF05, RD2.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0005 (ciclos de facturación), 0007 (reintentos de pagos fallidos).

---

