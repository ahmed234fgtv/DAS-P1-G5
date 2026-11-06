# 0007 — Obtención de datos y cálculo de las estadísticas de uso

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I exige que administradores y redactores puedan consultar cuatro métricas: **noticias más leídas, tiempo de permanencia, tasa de rebote y conversión de lectores a suscriptores**. No proporciona fórmulas, umbrales de tiempo ni define qué cuenta como visita, lectura o conversión. No podemos presentar esos criterios como requisitos ya fijados.

El problema arquitectónico es decidir **cómo disponer de datos de uso suficientes para calcular las cuatro métricas** y dónde situar la responsabilidad de su cálculo, evitando introducir un producto externo de analítica o un tercer almacén no prescrito.

El **MADR 0002** propone diferenciar peticiones mediante un identificador de sesión. Esa identificación es relevante para agrupar interacciones dentro de una visita al estudiar permanencia y rebote, pero **el identificador por sí solo no permite medir el tiempo ni determinar si hubo rebote**: deben existir datos temporales y una definición operativa de visita. Tampoco permite afirmar que dos sesiones pertenezcan a una misma persona. Para la conversión, el sistema deberá disponer de información mínima sobre la suscripción efectuada y el contexto de uso correspondiente, sin convertir ese vínculo técnico en identificación personal.

## Decision Drivers

* **RF07 — Estadísticas de noticias más leídas.**
* **RF08 — Estadísticas de tiempo de permanencia.**
* **RF09 — Estadísticas de tasa de rebote.**
* **RF10 — Estadísticas de conversión de lectores a suscriptores.**

## Considered Options

* **0007-1 — Registro de eventos mínimos de uso y cálculo mediante consultas.** Se representan hechos de navegación estrictamente necesarios (por ejemplo, consulta de noticia, referencia de sesión, momentos de interacción y alta de suscripción cuando proceda); un servicio de estadísticas calcula las métricas a partir de ellos.
* **0007-2 — Actualización incremental de métricas agregadas.** Cada interacción actualiza contadores y acumulados ya calculados; el módulo de estadísticas consulta los agregados en lugar de reconstruirlos desde eventos de detalle.

Ambas alternativas se plantean dentro del sistema; ninguna resuelve por sí sola las definiciones de métricas que el Anexo deja abiertas. La comparación se limita a **eventos de base frente a agregados mantenidos durante el uso**.

## Decision Outcome

Chosen option: "0007-1 — Registro mínimo de eventos de uso y cálculo mediante consultas", because conserva información de base con la que se pueden calcular o corregir las métricas cuando se precisen sus definiciones, sin fijar ahora umbrales de rebote o reglas de conversión que el Anexo no detalla. «Evento» es aquí una representación de datos de uso, **no** un sistema de mensajería ni un nuevo requisito funcional.

### Consequences

* Good, because los datos mínimos de uso pueden reutilizarse para obtener las cuatro métricas y revisar sus cálculos una vez se determinen sus definiciones operativas, relacionando interacciones de una sesión sin identificarlas como una persona única.
* Bad, because registrar interacciones individuales genera más volumen y exige decidir qué datos mínimos se conservan, mientras que los cálculos sobre el historial pueden ser más costosos que consultar agregados.
* **Pendiente de Rafa:** las definiciones concretas de visita, permanencia, rebote y conversión, así como la ubicación física de los datos no asignados en el Anexo.

### Confirmation

El UML deberá distinguir una responsabilidad para **registrar los datos mínimos de uso** y otra para **calcular o proporcionar las cuatro métricas** requeridas a administradores y redactores. El registro de interacción o visita tendrá una **referencia al identificador de sesión gestionado por el mecanismo del MADR 0002**, junto con los datos temporales necesarios; el cálculo de conversión tendrá acceso al hecho de alta de suscripción sin presuponer datos nominales del lector. Deben aparecer noticias más leídas, permanencia, rebote y conversión, sin fijar fórmulas o umbrales no especificados. La referencia de sesión agrupa interacciones, pero no equivale a identificar una persona. Este MADR no autoriza crear una tercera base de datos ni una política de identificación personal.

## Pros and Cons of the Options

### 0007-1 — Eventos mínimos y cálculo en consultas

* Good, because permite reconstruir el ranking de noticias leídas y, usando el identificador de sesión del MADR 0002 para relacionar eventos, recalcular permanencia, rebote o conversión cuando se precisen sus definiciones.
* Good, because un servicio estadístico separado concentra las reglas de cálculo y entrega las cuatro métricas a administradores y redactores, evitando repartir fórmulas por toda la aplicación.
* Bad, because conservar eventos de visita genera más registros que mantener solo contadores y exige delimitar cuáles son realmente necesarios y durante cuánto tiempo.
* Bad, because una consulta de estadísticas puede necesitar procesar numerosos eventos, por lo que el coste de cálculo crece con el historial registrado.

### 0007-2 — Actualización incremental de agregados

* Good, because las estadísticas solicitadas pueden consultarse rápidamente si el sistema mantiene acumulados de lecturas, duraciones, visitas y conversiones.
* Good, because evita conservar cada interacción de detalle cuando solo interesa el resultado agregado.
* Bad, because un error en el cálculo incremental de rebote o conversión es difícil de corregir sin los eventos originales.
* Bad, because cambiar la definición de «visita» o el criterio de permanencia obligaría a reiniciar o reconstruir los agregados, y el Anexo aún no precisa esas definiciones.

## Links

* Requisitos motivadores: RF07, RF08, RF09 y RF10 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Decisiones relacionadas: MADR 0002 (identificador de sesión) y MADR 0003 (altas de suscripción); se enlazan sus resultados sin añadir RF01, RF02 ni RF03 como drivers de este MADR.
