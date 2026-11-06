# 0005 — Límite de integración de los pagos online con Stripe

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El sistema debe gestionar pagos online y utilizar **Stripe** como pasarela. El Anexo fija el proveedor, pero no cómo deben depender de él las clases internas. Las operaciones de cobro necesitarán representar al menos el importe solicitado y el resultado obtenido; no se prescriben métodos de autenticación, webhooks, bibliotecas ni flujos adicionales.

La decisión consiste en fijar **dónde termina la lógica de pagos del periódico y dónde comienza la integración con Stripe**, para que las clases que coordinan cobros no conozcan necesariamente los detalles de la API externa.

## Decision Drivers

* **RF05 — Gestión de pagos online:** el sistema debe gestionar los pagos de las suscripciones.
* **RD2 — Pasarela de pago Stripe:** los pagos online deben gestionarse mediante Stripe.

## Considered Options

* **0005-1 — Puerto de pago propio y adaptador Stripe.** Un servicio de pagos depende de una interfaz con operaciones y resultados definidos por el periódico; un adaptador traduce las peticiones y respuestas de Stripe.
* **0005-2 — Integración directa con Stripe desde el servicio de pagos.** El servicio utiliza directamente la biblioteca/API de Stripe y contiene también la traducción de sus respuestas.

Ambas opciones utilizan obligatoriamente Stripe. No se contempla elegir otra pasarela, que sería incompatible con la restricción.

## Decision Outcome

Chosen option: "0005-1 — Puerto de pago propio y adaptador Stripe", because delimita una dependencia externa concreta y permite representar en UML una interfaz de pago cuyo contrato responde a las necesidades del periódico, mientras la clase adaptadora concentra las particularidades de Stripe. Esta elección **no implica** que vaya a añadirse otra pasarela.

### Consequences

* Good, because la lógica de facturación puede representar el resultado del cobro sin depender de tipos concretos de Stripe y la integración queda localizada en el adaptador.
* Bad, because hay que mantener un contrato de pago propio y comprobar que la traducción de respuestas no oculta información necesaria para distinguir pagos fallidos y pagos cuyo resultado aún no está confirmado.
* Este MADR no define la política de reintentos; esa responsabilidad corresponde al MADR 0006.

### Confirmation

El UML deberá mostrar un **servicio de pagos** que utiliza una **interfaz o puerto de pago**, implementado por un **adaptador de Stripe**, de forma que las dependencias de la lógica de aplicación apunten hacia el contrato propio y no directamente hacia clases del SDK externo. Se representará el resultado de pago necesario para distinguir éxito, fallo confirmado y resultado pendiente de confirmación, sin inventar un procedimiento técnico concreto de Stripe.

## Pros and Cons of the Options

### 0005-1 — Puerto de pago propio y adaptador Stripe

* Good, because las clases de facturación y suscripciones pueden operar sobre un resultado de pago del sistema sin importar directamente tipos externos de Stripe.
* Good, because el tratamiento del éxito, fallo y resultado no confirmado queda concentrado en un punto de traducción, lo que reduce el riesgo de aplicar respuestas inconsistentes en diferentes partes del back-end.
* Bad, because obliga a definir y mantener una interfaz y su correspondencia con las operaciones reales de Stripe, aunque el proyecto solo emplee una pasarela.
* Bad, because una interfaz demasiado genérica podría ocultar información que Stripe necesita devolver; el contrato debe limitarse a los casos exigidos y conservar los datos necesarios para gestionar el resultado.

### 0005-2 — Integración directa desde el servicio de pagos

* Good, because tiene menos piezas y permite utilizar sin traducción las capacidades que ofrece Stripe para tramitar el cobro exigido.
* Good, because simplifica el recorrido de la operación: el servicio inicia la petición y recibe la respuesta de la pasarela.
* Bad, because incorpora detalles del proveedor al servicio que también coordina las reglas de pago del periódico, mezclando dos responsabilidades.
* Bad, because si hay que corregir cómo se interpreta una respuesta de Stripe, el cambio se hace dentro de la lógica del servicio y aumenta el riesgo de afectar la facturación.

## Links

* Requisitos motivadores: RF05 y RD2 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
