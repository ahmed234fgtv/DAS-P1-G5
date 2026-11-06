# 0004 — Gestión de las bajas de suscripciones

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF03 exige permitir a los suscriptores darse de baja de sus suscripciones a noticias. El Anexo no precisa qué debe ocurrir con la información de la suscripción una vez dada de baja, ni si la baja debe poder revertirse.

El problema es decidir qué significa "dar de baja" en el modelo: si la relación de suscripción desaparece, si se conserva marcada, o si se representa como un cambio de estado. La tensión es entre la limpieza del modelo y la conservación de la historia, porque las métricas de conversión de lectores a suscriptores necesitan saber cuántos suscriptores hubo y cuándo dejaron de serlo.

## Decision Drivers

* **RF03 — Baja de suscripciones a noticias:** el sistema debe permitir a los suscriptores darse de baja de sus suscripciones a noticias.

## Considered Options

* 0004-1 — Borrado físico de la relación de suscripción.
* 0004-2 — Borrado lógico con marca temporal de baja.
* 0004-3 — Máquina de estados sobre la suscripción (activa, suspendida, dada de baja).
* 0004-4 — Baja registrada como evento en un histórico independiente.

## Decision Outcome

Chosen option: **0004-2 — Borrado lógico con marca temporal de baja**, because satisface RF03 conservando la información necesaria para las métricas sin introducir estados que ningún requisito del Anexo exige.

### Pros and Cons of the Options

#### 0004-2 — Borrado lógico con marca temporal de baja

* Good, because la suscripción deja de estar operativa de inmediato, que es lo que pide RF03, pero la fila se conserva con su fecha de baja y permite calcular cuántos lectores se convirtieron y cuántos abandonaron.
* Good, because es la opción con menos complejidad añadida: una marca temporal y un filtro en las consultas de suscripciones vigentes.
* Good, because permite revertir una baja por error sin reconstruir la relación desde cero.
* Bad, because todas las consultas de suscripciones vigentes deben recordar filtrar por la marca de baja, y olvidarlo en un punto produce entregas de noticias a suscriptores dados de baja.
* Bad, because la tabla crece de forma indefinida y hay que decidir si se depura periódicamente, lo que reabre la pérdida de historia que la opción pretendía evitar.

#### 0004-1 — Borrado físico

* Good, because el modelo queda limpio: no hay suscripciones inactivas ni filtros que recordar.
* Bad, because se pierde la información de cuántos suscriptores hubo, lo que impide calcular la tasa de conversión de lectores a suscriptores sobre periodos pasados.
* Bad, because una baja por error es irreversible y obliga al suscriptor a darse de alta otra vez, con un nuevo cobro inicial.

#### 0004-3 — Máquina de estados

* Good, because modela explícitamente todas las situaciones de la suscripción y deja el comportamiento de cada una en un sitio único.
* Bad, because el Anexo no define estados distintos de "vigente" y "dada de baja": introducir suspendida o pendiente de pago obligaría a inventar transiciones que ningún requisito respalda.
* Bad, because multiplica las clases y transiciones del diagrama sin que exista un requisito que las justifique, lo que el enunciado penaliza como arquitectura excesivamente complicada.

#### 0004-4 — Baja registrada como evento en un histórico

* Good, because conserva la trazabilidad completa y permitiría reconstruir el estado de cualquier suscripción en cualquier fecha.
* Bad, because exige mantener dos estructuras sincronizadas (la suscripción y su histórico) y decidir cuál es la fuente de verdad.
* Bad, because el coste de reconstruir el estado actual a partir de eventos es desproporcionado para una única transición como la baja.

## Consequences

* Good, because la baja es inmediata para el suscriptor y reversible para el sistema.
* Good, because la fecha de baja alimenta directamente las métricas de conversión de 0008.
* Bad, because hay que definir una convención única de filtrado de suscripciones vigentes y aplicarla en todos los servicios que consulten suscripciones.
* Bad, because si en el futuro se añade la suspensión por impago, esta decisión habrá que revisarla para adoptar 0004-3.

## Links

* Requisitos: RF03.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0003 (suscripciones a noticias), 0008 (estadísticas de uso).

---

