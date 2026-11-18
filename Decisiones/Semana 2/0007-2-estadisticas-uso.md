# 0007-2 — Obtención de datos y cálculo de las estadísticas de uso

* Status: proposed
* Date: 2026-11-11

## Context and Problem Statement

Los administradores y redactores deben poder consultar noticias más leídas, tiempo de permanencia, tasa de rebote y conversión de lectores a suscriptores. El Anexo I no fija fórmulas, umbrales ni define con precisión qué se considera lectura, visita o conversión. Por eso no se pueden presentar esas definiciones como requisitos impuestos por el profesor.

El problema arquitectónico es decidir qué datos mínimos se necesitan para calcular las cuatro métricas y en qué parte del sistema se realiza el cálculo. El identificador de sesión propuesto en el MADR 0002-2 permite agrupar interacciones, pero no basta por sí solo para medir duraciones o rebotes. Deben existir eventos temporales de navegación y una referencia al alta de suscripción cuando proceda. La decisión no añade un servicio externo de analítica ni una tercera base de datos.

## Decision Drivers

* **RF08 — Estadísticas de noticias más leídas:** administradores y redactores deben poder identificar las noticias más leídas.
* **RF09 — Estadísticas de tiempo de permanencia:** deben poder consultar el tiempo de permanencia de los usuarios.
* **RF10 — Estadísticas de tasa de rebote:** deben poder consultar las tasas de rebote.
* **RF11 — Estadísticas de conversión:** deben poder consultar la conversión de lectores a suscriptores.

## Considered Options

* **Opción A — Registro mínimo de interacciones y cálculo al consultar.** Se guardan hechos de uso, como apertura de noticia, sesión y momentos de interacción, y un servicio de estadísticas calcula las métricas a partir de ellos.
* **Opción B — Actualización de acumulados durante la navegación.** Cada interacción actualiza contadores y duraciones agregadas que el módulo de estadísticas consulta directamente.

## Decision Outcome

Chosen option: "Opción A — Registro mínimo de interacciones y cálculo al consultar", because permite conservar la información de base para revisar las cuatro métricas cuando se concreten las definiciones de visita, permanencia y conversión. Un registro de interacción es un dato del sistema, no una decisión de implantar mensajería ni de recopilar datos personales.

### Consequences

* Good, because pueden calcularse las noticias más leídas y agruparse interacciones por sesión para obtener permanencia y rebote.
* Good, because el hecho de alta de suscripción puede utilizarse al calcular conversiones sin exigir identificar nominalmente al lector.
* Bad, because registrar interacciones individuales ocupa más espacio y exige acotar la información conservada.
* Bad, because el cálculo sobre registros históricos puede ser más costoso que consultar valores acumulados.

### Confirmation

El UML debe distinguir las responsabilidades de registrar información de uso y proporcionar estadísticas a administradores y redactores. La representación de una interacción debe permitir conocer la noticia consultada, una referencia de sesión y datos de tiempo suficientes para evaluar la permanencia; la conversión requiere acceso al hecho de suscripción. Una sesión no equivale a una persona única. Deben quedar reflejadas las cuatro métricas sin presentar como obligatorios valores o fórmulas que no aparecen en los requisitos. No se decidirá aquí la ubicación de datos que el Anexo no asigna a una de las dos bases de datos.

## Pros and Cons of the Options

### Opción A — Registro mínimo de interacciones y cálculo al consultar

* Good, because puede rehacerse el ranking de noticias y recalcular las métricas al precisar su definición operativa.
* Good, because concentra en un módulo la obtención de resultados para administradores y redactores.
* Bad, because mantiene más registros que una solución basada solo en agregados.
* Bad, because para medir permanencia en la última página de una visita puede faltar un evento de salida, circunstancia que debe tratarse al concretar la medición.

### Opción B — Actualización de acumulados durante la navegación

* Good, because la consulta de los resultados estadísticos puede ser rápida porque los totales ya están calculados.
* Good, because puede evitar conservar el detalle de todas las interacciones si solo se necesitan métricas agregadas.
* Bad, because si cambia la forma de contar visitas o rebotes, los acumulados antiguos pueden no ser reconstruibles.
* Bad, because una actualización incorrecta en tiempo de navegación afecta a estadísticas posteriores sin disponer necesariamente del detalle para corregirla.

## Links

* Requisito(s) motivador(es): RF08, RF09, RF10, RF11 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0007](./0007-estadisticas-uso.md). La anulación anterior queda registrada como 0007-1.
* Decisiones relacionadas: MADR 0002-2 (agrupación por sesión) y MADR 0003-2 (altas de suscripción); las referencias no incorporan sus drivers.
