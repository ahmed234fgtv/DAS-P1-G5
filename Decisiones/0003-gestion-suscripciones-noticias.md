# 0003 — Gestión de las suscripciones a noticias

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF02 exige permitir a los lectores suscribirse a diferentes noticias. El Anexo describe un canal de suscripciones por el que el lector recibe las noticias que le interesan, y el sistema publica noticias de forma continua.

El problema es decidir cómo se representa la relación entre suscriptor y noticias y, sobre todo, cómo se propaga una noticia recién publicada hacia los suscriptores interesados, sin que el componente que publica noticias tenga que conocer a quién debe avisar. La tensión es de acoplamiento: cuantas más noticias y suscriptores, más costoso resulta que el publicador recorra la lista de interesados.

## Decision Drivers

* **RF02 — Suscripción a noticias:** el sistema debe permitir a los lectores suscribirse a diferentes noticias.

## Considered Options

* 0003-1 — Observer: el gestor de suscripciones se registra como observador de la publicación de noticias.
* 0003-2 — Consulta bajo demanda (pull): el suscriptor consulta periódicamente sus noticias.
* 0003-3 — Cola de mensajes con tópicos por categoría (pub/sub).
* 0003-4 — Envío directo desde el publicador a cada suscriptor.

## Decision Outcome

Chosen option: **0003-1 — Observer**, because desacopla la publicación de la entrega sin introducir infraestructura de mensajería que ningún requisito del Anexo exige.

### Pros and Cons of the Options

#### 0003-1 — Observer

* Good, because el componente que publica una noticia solo notifica a sus observadores y no necesita conocer la lista de suscriptores, de modo que añadir un tipo de suscriptor nuevo no obliga a modificar el publicador.
* Good, because la relación observador-observado se modela con clases del dominio, sin dependencias externas, lo que encaja en la capa de negocio del estilo de 0001.
* Good, because permite que el gestor de suscripciones decida a qué suscriptores afecta cada noticia según sus intereses, manteniendo esa lógica fuera del publicador.
* Bad, because la notificación es síncrona: si el envío a los suscriptores es lento, la operación de publicación se ralentiza, y hay que decidir si la entrega se hace en el mismo hilo o se encola.
* Bad, because el publicador mantiene referencias a sus observadores, y una baja mal gestionada deja observadores huérfanos que siguen recibiendo notificaciones.

#### 0003-2 — Consulta bajo demanda (pull)

* Good, because es la opción más simple: no hay notificaciones ni registros de observadores que mantener.
* Bad, because el suscriptor solo ve las noticias cuando consulta, de modo que la entrega deja de ser una acción del sistema y pasa a depender del comportamiento del cliente.
* Bad, because obliga a cada consulta a recorrer las noticias publicadas desde la última visita, lo que se encarece a medida que crece el archivo de noticias.

#### 0003-3 — Cola de mensajes con tópicos por categoría

* Good, because desacopla por completo publicación y entrega, y permitiría reintentar entregas fallidas sin afectar al publicador.
* Bad, because introduce un broker externo que no aparece en ningún requisito del Anexo y que habría que desplegar, configurar y documentar en el diagrama de arquitectura.
* Bad, because la entrega pasa a ser asíncrona y eventual, lo que complica explicar cuándo recibe el suscriptor una noticia recién publicada.

#### 0003-4 — Envío directo desde el publicador a cada suscriptor

* Good, because es inmediato y fácil de trazar: cada publicación genera exactamente un envío por suscriptor.
* Bad, because acopla el publicador a la lista de suscriptores y a su mecanismo de entrega, de modo que cualquier cambio en la forma de suscribirse obliga a modificar el publicador.
* Bad, because el coste de publicar crece linealmente con el número de suscriptores, y un fallo en un suscriptor interrumpe el envío al resto.

## Consequences

* Good, because el publicador de noticias queda cerrado a modificación cuando se añadan nuevas formas de suscripción.
* Good, because la lógica de "a quién le interesa esta noticia" queda concentrada en el gestor de suscripciones.
* Bad, because hay que definir la política de baja de observadores para evitar notificaciones a suscriptores dados de baja.
* Bad, because si la entrega se hace en el mismo hilo de publicación, el tiempo de respuesta de la publicación depende del número de suscriptores.

## Links

* Requisitos: RF02.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0004 (bajas de suscripciones), 0005 (ciclos de facturación).

---

