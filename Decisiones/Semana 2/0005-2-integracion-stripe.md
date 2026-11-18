# 0005-2 — Límite de integración de los pagos online con Stripe

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

El periódico debe gestionar pagos online de las suscripciones y emplear Stripe como pasarela. Los requisitos fijan el propósito del módulo y el proveedor externo, pero no determinan cómo organizar la dependencia de Stripe dentro del back-end. Una operación de pago tiene que poder comunicar el importe solicitado y el resultado del cobro; el Anexo no define una biblioteca, un protocolo interno adicional ni un procedimiento de confirmación concreto.

La cuestión de diseño es separar, o no, la lógica del periódico que solicita un cobro de los detalles técnicos necesarios para utilizar Stripe. No es una decisión sobre qué pasarela escoger: Stripe ya viene impuesta por RD2.

## Decision Drivers

* **RF06 — Gestión de pagos online:** el sistema debe gestionar los pagos online de las suscripciones.
* **RD2 — Pasarela de pago Stripe:** los pagos online deben gestionarse mediante Stripe.

## Considered Options

* **Opción A — Puerto de pagos propio con adaptador Stripe.** La lógica de aplicación utiliza un contrato de pago del periódico; un adaptador separado traduce las solicitudes y respuestas de Stripe.
* **Opción B — Integración directa con Stripe desde el servicio de pagos.** El mismo servicio que coordina el pago utiliza directamente las operaciones y respuestas de Stripe.

## Decision Outcome

Chosen option: "Opción A — Puerto de pagos propio con adaptador Stripe", because mantiene la responsabilidad de coordinar el cobro separada del código que se comunica con la pasarela exigida. Esta división se puede mostrar en los paquetes de lógica e integración y no supone incorporar proveedores alternativos.

### Consequences

* Good, because las particularidades de Stripe quedan localizadas en el adaptador, en lugar de extenderse por el módulo de facturación.
* Good, because la lógica de aplicación recibe un resultado de pago expresado con conceptos del periódico.
* Bad, because es necesario mantener un contrato interno y traducir correctamente los estados devueltos por Stripe, lo que añade una pieza de diseño.

### Confirmation

El UML debe distinguir el componente o paquete de pagos de aplicación del adaptador de Stripe, situado en integración. Puede mostrarse una interfaz de pago propia y una clase adaptadora dentro de sus respectivos paquetes; el profesor ha indicado que las dependencias se representen entre paquetes, no entre clases. La comunicación externa se dirige a Stripe. El diseño debe contemplar resultados satisfactorios, fallidos y no confirmados, sin fijar flujos técnicos que el Anexo no impone. La limitación de reintentos corresponde al MADR 0006-2.

## Pros and Cons of the Options

### Opción A — Puerto de pagos propio con adaptador Stripe

* Good, because la lógica de pagos no queda escrita en términos de clases o respuestas propias de Stripe.
* Good, because una corrección en la interpretación de un resultado de la pasarela se concentra en integración.
* Bad, because el contrato interno debe definir qué datos devuelve, para no ocultar un fallo o un resultado todavía no confirmado.
* Bad, because requiere un adaptador y una interfaz aunque solo exista un proveedor autorizado.

### Opción B — Integración directa con Stripe desde el servicio de pagos

* Good, because reduce el número de elementos necesarios para iniciar el cobro en el caso actual.
* Good, because permite utilizar los resultados del proveedor sin una traducción intermedia.
* Bad, because mezcla en una misma responsabilidad la coordinación del cobro y los detalles de la API externa.
* Bad, because un cambio en la interpretación de respuestas de Stripe obliga a modificar el servicio de aplicación que también coordina las operaciones de pago.

## Links

* Requisito(s) motivador(es): RF06, RD2 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0005](./0005-integracion-stripe.md). La anulación anterior queda registrada como 0005-1.
* Decisiones relacionadas: MADR 0004-2 (ciclos de facturación) y MADR 0006-2 (reintentos); sus requisitos no se repiten aquí como drivers.
