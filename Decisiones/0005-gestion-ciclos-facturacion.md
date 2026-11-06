# 0005 — Gestión de los ciclos de facturación

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF04 exige gestionar los ciclos de facturación mediante tres modalidades: trimestral, semestral y anual. Cada modalidad combina una periodicidad y un importe distintos, y el sistema debe determinar cuándo vence el siguiente cobro de una suscripción y por cuánto.

El problema es decidir dónde reside esa variación. Si el cálculo del vencimiento y del importe se dispersa en condicionales por el código de suscripciones y de pagos, añadir o modificar una modalidad obliga a localizar y tocar varios puntos, con riesgo de que queden incoherentes entre sí.

## Decision Drivers

* **RF04 — Gestión de modalidades de facturación:** el sistema debe gestionar los ciclos de facturación de las suscripciones mediante tres modalidades: trimestral, semestral y anual.

## Considered Options

* 0005-1 — Strategy: una estrategia por modalidad que encapsula periodicidad e importe.
* 0005-2 — Condicionales sobre un enumerado de modalidad en el servicio de facturación.
* 0005-3 — Jerarquía de subclases de suscripción (trimestral, semestral, anual).
* 0005-4 — Tabla de configuración en base de datos leída en tiempo de ejecución.

## Decision Outcome

Chosen option: **0005-1 — Strategy**, because concentra la variación de cada modalidad en un único punto y evita que el cálculo del vencimiento y del importe se repita en cada operación de facturación.

### Pros and Cons of the Options

#### 0005-1 — Strategy

* Good, because cada modalidad encapsula su periodo y su importe, de modo que el servicio de facturación pide "el siguiente vencimiento" sin conocer si la suscripción es trimestral o anual.
* Good, because añadir una modalidad nueva consiste en añadir una estrategia, sin tocar el código que ya funciona para las tres existentes.
* Good, because la misma estrategia sirve para el cobro inicial y para los cobros de renovación, evitando duplicar el cálculo en dos operaciones distintas.
* Bad, because introduce una interfaz y tres implementaciones donde un condicional bastaría, lo que engorda el diagrama de clases para un conjunto de modalidades que el Anexo fija en tres.
* Bad, because la estrategia debe recibir la fecha del último cobro para calcular el siguiente vencimiento, lo que obliga a que el servicio de facturación le pase un dato que no forma parte de la modalidad.

#### 0005-2 — Condicionales sobre un enumerado

* Good, because es la solución más directa y no añade clases al diagrama.
* Bad, because el cálculo de vencimiento y de importe aparece en el cobro inicial y en el de renovación, y cualquier cambio de importe obliga a localizar todas las apariciones del condicional.
* Bad, because la lógica de cada modalidad queda mezclada con el flujo de facturación, lo que hace ilegible el método que decide cuándo cobrar.

#### 0005-3 — Jerarquía de subclases de suscripción

* Good, because la modalidad forma parte de la identidad de la suscripción y el polimorfismo resuelve el cálculo sin condicionales.
* Bad, because una suscripción que cambia de modalidad obligaría a destruir el objeto y crear otro de otra clase, cuando el cambio de modalidad es una operación de negocio normal.
* Bad, because mezcla la variación de la facturación con la entidad suscripción, que tiene otras responsabilidades, y dificulta reutilizar el cálculo en el cobro inicial.

#### 0005-4 — Tabla de configuración en base de datos

* Good, because permitiría cambiar importes sin recompilar ni desplegar.
* Bad, because introduce una dependencia de la base de datos en una regla de negocio, lo que obliga a que el cálculo del vencimiento consulte la base en cada operación.
* Bad, because los importes son datos fijos del Anexo y no se prevé que cambien durante la práctica, de modo que la flexibilidad no se usa.

## Consequences

* Good, because el servicio de facturación queda libre de condicionales por modalidad.
* Good, because las tres estrategias son la única fuente del importe y del periodo, lo que evita incoherencias entre el cobro inicial y el de renovación.
* Bad, because hay que definir el contrato de la estrategia (qué recibe y qué devuelve) antes de modelar el diagrama de clases.
* Bad, because la fecha del último cobro debe persistirse en la suscripción para que la estrategia pueda calcular el siguiente vencimiento.

## Links

* Requisitos: RF04.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0006 (integración de pagos), 0007 (reintentos de pagos fallidos).

---

