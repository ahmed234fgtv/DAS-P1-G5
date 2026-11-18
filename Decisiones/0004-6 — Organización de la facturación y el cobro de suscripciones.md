# 0004-6 — Organización de la facturación y el cobro de suscripciones

* Status: proposed
* Date: 2026-11-18

## Context and Problem Statement

El periódico debe gestionar tres modalidades de facturación: trimestral (70 € cada tres meses), semestral (130 € cada seis meses) y anual (190 € cada doce meses). El pago se realiza mediante Stripe y, si falla, el sistema debe permitir hasta tres reintentos. El Anexo no fija porcentajes de descuento, ni exige que los reintentos sean automáticos. La modalidad anual también se renueva: no es un pago definitivo sin vencimiento posterior.

Para cumplir estas condiciones hay que coordinar la modalidad elegida, la fecha del siguiente cobro, el resultado devuelto por Stripe y los reintentos de una misma operación. La decisión es **cómo distribuir esas responsabilidades dentro del back-end**, de forma que el límite de reintentos y los importes se apliquen siempre de manera coherente. Stripe, las tres modalidades y las cantidades son obligatorios; no se eligen aquí. Ambas opciones respetan el estilo de capas definido en el MADR [0001-6](./0001-6%20%E2%80%94%20Selecci%C3%B3n%20del%20estilo%20arquitect%C3%B3nico%20del%20peri%C3%B3dico%20digital.md).

## Decision Drivers

* **RF05 — Gestión de modalidades de facturación:** tres modalidades, con importes de 70 €, 130 € y 190 € y periodicidades de tres, seis y doce meses.
* **RF06 — Gestión de pagos online:** el sistema debe gestionar los pagos de las suscripciones.
* **RF07 — Reintentos de pagos fallidos:** se deben permitir hasta tres reintentos cuando una petición haya fallado.
* **RD2 — Pasarela de pago Stripe:** el cobro online debe realizarse mediante Stripe.

## Considered Options

* **Opción A — Servicio de cobro único en la lógica de aplicación.** Un mismo servicio determina la modalidad, el importe y el vencimiento, solicita el cobro a través de la integración con Stripe y mantiene el contador de reintentos. El adaptador externo sigue separado, pero las reglas de facturación y los intentos de pago se concentran en el mismo servicio.
* **Opción B — Responsabilidades de facturación y pagos separadas dentro del back-end.** Un componente de facturación reúne las tres modalidades y calcula el vencimiento; otro coordina el cobro y controla los reintentos de la operación. La comunicación con Stripe se concentra en un adaptador de integración. No son servicios desplegados por separado, sino responsabilidades internas de la arquitectura elegida.

## Decision Outcome

Chosen option: "Opción B — Responsabilidades de facturación y pagos separadas dentro del back-end", because permite tratar los importes y vencimientos sin mezclarlos con los resultados de la pasarela, y deja el límite de tres reintentos junto a la operación de pago que debe controlarlos. No hace falta que las clases de facturación conozcan las respuestas de Stripe y tampoco se añade un proceso de cobro independiente del back-end.

Las dos opciones utilizan el patrón Adapter para concentrar la integración con Stripe. El adaptador traduce las peticiones de cobro y las respuestas de la pasarela, de modo que los detalles de Stripe quedan en la integración. En el [MADR 0004-2](./0004-2-modalidades-facturacion.md) se valoró Strategy. Se descartó porque las modalidades tienen importes y periodos distintos y comparten la misma regla de vencimiento.

### Consequences

* Good, because las tres modalidades se definen en un solo lugar con su importe y periodo correspondiente, sin repetir esos datos al gestionar cada intento de pago.
* Good, because el resultado del cobro y el contador de reintentos quedan bajo la responsabilidad de pagos, mientras el adaptador reúne los detalles técnicos de Stripe.
* Bad, because hay más responsabilidades internas que coordinar que en un servicio único. Debe quedar claro qué componente solicita el cobro y cuál actualiza su estado.
* Bad, because los reintentos deben asociarse a la misma operación y no iniciarse sin comprobar antes si un cobro de resultado desconocido llegó a completarse.

### Confirmation

Revisaremos un cobro de cada modalidad para comprobar el importe, el vencimiento y el uso del adaptador de Stripe. En un caso de pago fallido, comprobaremos que los reintentos se asocian a la misma operación y que se permiten hasta tres.

## Pros and Cons of the Options

### Opción A — Servicio de cobro único en la lógica de aplicación

* Good, because el recorrido desde la modalidad elegida hasta la petición de cobro y sus reintentos se concentra en un único punto de coordinación.
* Good, because necesita menos componentes internos y puede resultar sencillo mientras solo existan tres modalidades y una pasarela.
* Bad, because el servicio acabaría reuniendo reglas de vencimiento con la interpretación de resultados de pago y el control de sus fallos.
* Bad, because un cambio en el control de reintentos podría obligar a modificar el mismo servicio que calcula fechas e importes, aunque esas reglas no hayan cambiado.

### Opción B — Responsabilidades de facturación y pagos separadas dentro del back-end

* Good, because los importes y periodos quedan en facturación y no tienen que repetirse en cada intento enviado a Stripe.
* Good, because el control de los tres reintentos y la interpretación del resultado de cobro permanecen en pagos, sin trasladar esa responsabilidad al módulo de facturación.
* Good, because la integración con Stripe conserva un límite claro y el código de facturación no depende de sus tipos de respuesta.
* Bad, because requiere coordinar el vencimiento calculado con la operación de cobro, en lugar de resolver ambas tareas dentro de un único servicio.
* Bad, because exige delimitar las responsabilidades para evitar que facturación y pagos mantengan dos contadores o dos estados distintos de la misma operación.

## Links

* Requisitos motivadores: RF05, RF06, RF07 y RD2 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye al [MADR 0004-4](./0004-4-facturacion-cobro.md), según consta en la [anulación 0004-5](./0004-5%20Anulamos%20Organizaci%C3%B3n%20de%20la%20facturaci%C3%B3n%20y%20el%20cobro%20de%20suscripciones.md).
* Decisión relacionada: MADR [0001-6](./0001-6%20%E2%80%94%20Selecci%C3%B3n%20del%20estilo%20arquitect%C3%B3nico%20del%20peri%C3%B3dico%20digital.md), que establece la estructura de capas; este MADR concreta la distribución interna de las responsabilidades del cobro.
