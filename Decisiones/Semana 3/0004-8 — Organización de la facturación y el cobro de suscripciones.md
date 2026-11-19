# 0004-8 — Organización de la facturación y el cobro de suscripciones

* Status: proposed
* Date: 2026-11-19

## Context and Problem Statement

El periódico debe gestionar tres modalidades de facturación: trimestral (70 € cada tres meses), semestral (130 € cada seis meses) y anual (190 € cada doce meses). El pago se realiza mediante Stripe y, si falla, el sistema debe permitir hasta tres reintentos. El Anexo no fija porcentajes de descuento, ni exige que los reintentos sean automáticos. La modalidad anual también se renueva: no es un pago definitivo sin vencimiento posterior.

La decisión es si conviene tratar conjuntamente las reglas de las modalidades y el seguimiento de cada operación, o mantener ambos aspectos separados. Las dos alternativas respetan las modalidades, los importes, el uso de Stripe y el límite de reintentos establecidos para el periódico. Si el resultado de un intento queda sin confirmar, existe el riesgo de repetir una operación que ya se completó.

## Decision Drivers

* **RF05 — Gestión de modalidades de facturación:** tres modalidades, con importes de 70 €, 130 € y 190 € y periodicidades de tres, seis y doce meses.
* **RF06 — Gestión de pagos online:** el sistema debe gestionar los pagos de las suscripciones.
* **RF07 — Reintentos de pagos fallidos:** se deben permitir hasta tres reintentos cuando una petición haya fallado.
* **RD2 — Pasarela de pago Stripe:** el cobro online debe realizarse mediante Stripe.

## Considered Options

* **Opción A — Gestión unificada de la facturación y el cobro.** Una misma responsabilidad abarca el importe, el vencimiento, el resultado de la operación y sus reintentos.
* **Opción B — Gestión separada de la facturación y el cobro.** Las reglas de importe y vencimiento se distinguen del seguimiento de cada operación, que incluye su resultado y sus reintentos.

## Decision Outcome

Chosen option: "Opción B — Gestión separada de la facturación y el cobro", because un cambio en el tratamiento de un intento fallido puede abordarse sin revisar las reglas que determinan cuánto se cobra y cuándo vence cada modalidad.

### Consequences

* Good, because las reglas de importe y vencimiento de las tres modalidades se pueden mantener aunque cambie el tratamiento de una operación fallida.
* Bad, because hay que mantener la coherencia entre la modalidad elegida y la operación a la que se aplican su importe y su vencimiento.

### Confirmation

Comprobaremos que para las modalidades trimestral, semestral y anual se aplican respectivamente 70 € cada tres meses, 130 € cada seis meses y 190 € cada doce meses, y que la operación se realiza mediante Stripe. Si falla el intento inicial, comprobaremos que se permiten como máximo tres reintentos de esa misma operación.

## Pros and Cons of the Options

### Opción A — Gestión unificada de la facturación y el cobro

* Good, because concentra en una sola responsabilidad las reglas de la modalidad y el seguimiento de la operación, lo que reduce la coordinación entre ambas.
* Bad, because un cambio en los reintentos puede afectar al mismo ámbito donde se determinan los importes y los vencimientos.

### Opción B — Gestión separada de la facturación y el cobro

* Good, because se puede revisar el tratamiento de los fallos sin replantear las reglas de las modalidades trimestral, semestral y anual.
* Bad, because hay que coordinar el importe y el vencimiento de la modalidad elegida con la operación correspondiente.

## Links

* Requisitos motivadores: RF05, RF06, RF07 y RD2 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye al [MADR 0004-6](<./0004-6 — Organización de la facturación y el cobro de suscripciones.md>), según consta en la [anulación 0004-7](<./0004-7 Anulamos Organización de la facturación y el cobro de suscripciones.md>).
* Decisión relacionada: [MADR 0001-8](<./0001-8 — Selección del estilo arquitectónico del periódico digital.md>), que establece el estilo arquitectónico.