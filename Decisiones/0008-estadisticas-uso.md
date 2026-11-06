# 0008 — Obtención y presentación de las estadísticas de uso

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF07, RF08, RF09 y RF10 exigen proporcionar a administradores y redactores cuatro métricas: noticias más leídas, tiempo de permanencia, tasa de rebote y conversión de lectores a suscriptores. Las cuatro se calculan sobre el mismo conjunto de datos —las lecturas y las altas de suscripción— pero cada una los agrega de forma distinta.

El problema es decidir cuándo se calculan. Un cálculo bajo demanda da siempre el dato actualizado pero recorre todo el histórico en cada consulta; una preagregación periódica responde rápido pero sirve datos que pueden estar desfasados. La decisión afecta a qué se guarda y a cuánto tarda en responder la interfaz de administración.

## Decision Drivers

* **RF07 — Estadísticas de noticias más leídas:** el sistema debe proporcionar a los administradores y redactores estadísticas que permitan identificar las noticias más leídas.
* **RF08 — Estadísticas de tiempo de permanencia:** el sistema debe proporcionar a los administradores y redactores estadísticas sobre el tiempo de permanencia de los usuarios.
* **RF09 — Estadísticas de tasa de rebote:** el sistema debe proporcionar a los administradores y redactores estadísticas sobre las tasas de rebote.
* **RF10 — Estadísticas de conversión:** el sistema debe proporcionar a los administradores y redactores estadísticas sobre la conversión de lectores a suscriptores.

## Considered Options

* 0008-1 — Cálculo bajo demanda con consultas agregadas sobre los datos de lecturas y suscripciones.
* 0008-2 — Preagregación periódica en tablas de resumen.
* 0008-3 — Agregación incremental en memoria al registrar cada lectura.
* 0008-4 — Motor externo de analítica.

## Decision Outcome

Chosen option: **0008-1 — Cálculo bajo demanda**, because las cuatro métricas comparten la misma fuente de datos y el volumen de un periódico de práctica no justifica mantener estructuras de resumen que habría que sincronizar.

### Pros and Cons of the Options

#### 0008-1 — Cálculo bajo demanda

* Good, because las cuatro métricas se calculan sobre los mismos datos con consultas distintas, de modo que no hay estructuras duplicadas que puedan quedar desincronizadas entre sí.
* Good, because el dato que ve el administrador es siempre el actual, sin desfase entre la última lectura y el informe.
* Good, because permite filtrar por periodo y por categoría en el momento de la consulta, sin necesidad de prever por adelantado las combinaciones que se van a pedir.
* Bad, because cada consulta recorre el histórico completo de lecturas, y el tiempo de respuesta crece con el volumen de tráfico del periódico.
* Bad, because las cuatro métricas se calculan en cuatro consultas distintas, y la interfaz de administración tendrá que esperar a que terminen todas.

#### 0008-2 — Preagregación periódica

* Good, because las consultas de la interfaz de administración leen una tabla de resumen pequeña y responden en tiempo constante.
* Bad, because el dato que ve el administrador corresponde al último cierre, de modo que las lecturas del día en curso no aparecen en el ranking de noticias más leídas.
* Bad, because hay que mantener una tarea de agregación y decidir su frecuencia, y cada métrica necesita su propia tabla de resumen.

#### 0008-3 — Agregación incremental en memoria

* Good, because el dato está disponible al instante y la consulta es una simple lectura de contadores.
* Bad, because los contadores viven en memoria del proceso y se pierden al reiniciar, lo que hace que las métricas dependan de la estabilidad del servidor.
* Bad, because en un despliegue con más de una instancia cada una mantendría sus propios contadores y el resultado sería la suma incoherente de varios conjuntos.

#### 0008-4 — Motor externo de analítica

* Good, because ofrecería consultas complejas y visualizaciones sin programarlas.
* Bad, because introduce un componente externo que ningún requisito del Anexo exige y que habría que desplegar y justificar en el diagrama de arquitectura.
* Bad, because obliga a exportar los datos de lecturas a otro sistema, con la consiguiente duplicación de la información y la necesidad de decidir cada cuánto se sincroniza.

## Consequences

* Good, because no hay estructuras de resumen que mantener ni tareas programadas asociadas a las métricas.
* Good, because el filtrado por periodo y por categoría se resuelve en la propia consulta.
* Bad, because el rendimiento de la interfaz de administración depende del volumen de lecturas registradas, y habrá que revisar la decisión si el tráfico crece.
* Bad, because las cuatro métricas deben compartir una definición única de "lectura" y de "periodo", o los resultados serán incoherentes entre sí.

## Links

* Requisitos: RF07, RF08, RF09, RF10.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0002 (identificación de clientes), 0004 (bajas de suscripciones).
