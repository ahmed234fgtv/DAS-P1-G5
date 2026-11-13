# 0004-2 — Representación de las modalidades y ciclos de facturación

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

El sistema debe gestionar tres modalidades de facturación: trimestral, 70 € cada tres meses; semestral, 130 € cada seis meses; y anual, 190 € cada doce meses. El Anexo I califica los descuentos como base, intermedio y de mayor ahorro por mes, pero no fija porcentajes para calcularlos. La modalidad anual supone un pago cada doce meses, no una compra sin renovación.

Hay que decidir cómo representar los importes y periodos de estas tres modalidades y cómo determinar los vencimientos sin repartir esas reglas por distintas partes del back-end. No se plantea crear más tarifas, permitir su edición desde un panel ni suponer una fórmula de descuento adicional.

## Decision Drivers

* **RF05 — Gestión de modalidades de facturación:** deben gestionarse las modalidades trimestral (70 €/3 meses), semestral (130 €/6 meses) y anual (190 €/12 meses).

## Considered Options

* **Opción A — Catálogo de modalidades y regla común de vencimiento.** Cada modalidad reúne importe y periodo en meses, y un servicio de facturación utiliza una operación común para calcular la siguiente fecha.
* **Opción B — Patrón Strategy con una clase por modalidad.** Cada modalidad dispone de una estrategia que proporciona su importe y calcula su siguiente vencimiento.
* **Opción C — Condicionales dentro del servicio de facturación.** El servicio determina importes y vencimientos con ramas distintas para el tipo trimestral, semestral o anual.

## Decision Outcome

Chosen option: "Opción A — Catálogo de modalidades y regla común de vencimiento", because el Anexo diferencia las tres modalidades por importes y periodos, pero no por métodos de cálculo distintos. Reunir esos valores en un catálogo facilita comprobarlos y evita crear tres clases que ejecutarían esencialmente la misma operación.

### Consequences

* Good, because los periodos y precios del Anexo quedan reunidos en un único modelo de modalidad.
* Good, because el cálculo del vencimiento se expresa como una responsabilidad común sin duplicar las reglas por tarifa.
* Bad, because si en el futuro se exigieran reglas distintas para alguna modalidad, habría que revisar la operación común; ese caso no se desprende del Anexo.

### Confirmation

El UML debe incluir una representación de modalidad de facturación con importe y periodo, así como un componente encargado de aplicar el ciclo y obtener el siguiente vencimiento. Debe poder comprobarse que existen exactamente las tres definiciones exigidas: 3 meses/70 €, 6 meses/130 € y 12 meses/190 €. No se introducirán clases para porcentajes de descuento no definidos ni planes adicionales. Las dependencias arquitectónicas se expresarán entre paquetes.

## Pros and Cons of the Options

### Opción A — Catálogo de modalidades y regla común de vencimiento

* Good, because los valores concretos de las tres tarifas pueden contrastarse directamente con el RF05.
* Good, because la misma operación de facturación sirve para los tres periodos expresados en meses.
* Bad, because debe impedirse una combinación inválida, como asignar 190 € al periodo de tres meses.
* Bad, because la regla común no cubre de forma automática condiciones especiales que no figuran en el alcance actual.

### Opción B — Patrón Strategy con una clase por modalidad

* Good, because permite que cada periodo tenga un cálculo propio si las modalidades fueran a diferenciarse en su comportamiento.
* Good, because el servicio de facturación podría depender de una interfaz de estrategia en vez de condicionales.
* Bad, because las tres implementaciones sumarían meses y devolverían un importe fijo, duplicando comportamiento para el caso actual.
* Bad, because incrementa el número de elementos UML sin que el Anexo identifique reglas de vencimiento distintas.

### Opción C — Condicionales dentro del servicio de facturación

* Good, because es directo para tres casos de importe y periodo ya conocidos.
* Good, because no necesita una estructura adicional de modalidades.
* Bad, because mezcla datos de las tarifas con el cálculo, dificultando distinguir esas responsabilidades.
* Bad, because si importe y periodo se consultan desde más de una operación, pueden aparecer condicionales repetidos con riesgo de incoherencia.

## Links

* Requisito(s) motivador(es): RF05 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0004](./0004-modalidades-facturacion.md). La anulación anterior queda registrada como 0004-1.
* Decisiones relacionadas: MADR 0005-2, encargado de la integración del cobro sin volver a tomar RF05 como driver.
