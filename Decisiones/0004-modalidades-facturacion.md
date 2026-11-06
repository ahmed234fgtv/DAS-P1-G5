# 0004 — Representación de las modalidades y ciclos de facturación

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I fija tres modalidades para las suscripciones de pago: **trimestral, 70 € cada tres meses; semestral, 130 € cada seis meses; y anual, 190 € cada doce meses**. Describe descuentos relativos entre ellas, pero no proporciona porcentajes de descuento que deban calcularse. La modalidad anual se describe como un pago único que se repite cada doce meses; no debe interpretarse como pago definitivo sin renovación.

Hay que decidir cómo representar el **importe y el periodo** de cada modalidad y cómo obtener el siguiente vencimiento, evitando dispersar estos valores en las clases que solicitan los cobros.

## Decision Drivers

* **RF04 — Gestión de modalidades de facturación:** el sistema debe gestionar los ciclos trimestral, semestral y anual.

## Considered Options

* **0004-1 — Catálogo de modalidades con una regla común de cálculo.** Tres definiciones de modalidad reúnen importe y número de meses; un servicio de facturación utiliza una operación común para calcular el siguiente vencimiento.
* **0004-2 — Patrón Strategy con una implementación por modalidad.** Cada estrategia conoce el importe y el cálculo del ciclo de su modalidad y es seleccionada por el servicio de facturación.
* **0004-3 — Condicionales por modalidad dentro del servicio de facturación.** El servicio contiene directamente los importes y periodos y selecciona la rama correspondiente a cada tipo.

Todas las alternativas representan las tres modalidades; ninguna plantea añadir planes nuevos como requisito.

## Decision Outcome

Chosen option: "0004-1 — Catálogo de modalidades con regla común", because las tres modalidades difieren, según el Anexo, solo en **importe y número de meses**, y pueden compartir la misma operación de cálculo sin introducir una jerarquía de clases específica ni duplicar valores dentro de condicionales.

### Consequences

* Good, because los tres importes y periodos exigidos se conservan en un único modelo de modalidad, utilizable por el cálculo del vencimiento.
* Bad, because si aparecieran reglas particulares distintas de la suma de meses habría que revisar la operación común; el Anexo actual no las establece.
* No se deducen porcentajes de descuento ni reglas de cambio de modalidad que no aparecen en el texto.

### Confirmation

En UML se comprobará que la **modalidad de facturación** reúne el importe y el periodo en meses, y que una responsabilidad de **facturación** obtiene el siguiente vencimiento mediante una regla común. Las tres modalidades deberán corresponder exactamente a **3 meses/70 €, 6 meses/130 € y 12 meses/190 €**. No deberán aparecer clases por supuestos descuentos porcentuales ni un mecanismo de administración de tarifas que no se pide.

## Pros and Cons of the Options

### 0004-1 — Catálogo de modalidades con regla común

* Good, because los tres importes y las tres periodicidades quedan definidos en un único lugar verificable frente al Anexo.
* Good, because el diagrama necesita una representación de **ModalidadFacturación** y un servicio de cálculo, sin tres clases que repitan esencialmente el mismo comportamiento.
* Bad, because si una modalidad adquiriera reglas especiales no descritas ahora, la operación común tendría que revisarse o delegarse en estrategias.
* Bad, because hay que evitar que una modalidad configurada incorrectamente contenga, por ejemplo, el importe anual con un periodo trimestral.

### 0004-2 — Strategy con una implementación por modalidad

* Good, because cada plan encapsula sus cálculos y permite introducir reglas de vencimiento diferenciadas sin tocar la implementación de los otros planes.
* Good, because las clases de facturación dependerían de una interfaz estable de cálculo.
* Bad, because, con los únicos parámetros exigidos (meses e importe), tres implementaciones repetirían la misma operación de sumar meses y devolver un precio fijo.
* Bad, because aumenta interfaces y relaciones del UML sin aportar una variación de comportamiento justificada por el Anexo actual.

### 0004-3 — Condicionales dentro del servicio de facturación

* Good, because es simple de implementar y no requiere un catálogo de objetos adicional.
* Good, because las tres ramas pueden comprobarse directamente contra los valores del enunciado.
* Bad, because mezcla datos del plan con las operaciones de facturación; si varias operaciones necesitan saber el periodo o importe, tenderán a reproducir el mismo condicional.
* Bad, because dificulta representar las modalidades como conceptos diferenciados en el modelo de clases.

## Links

* Requisito motivador: RF04 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
