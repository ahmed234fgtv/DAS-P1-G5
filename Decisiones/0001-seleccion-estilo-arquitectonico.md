# 0001 — Selección del estilo arquitectónico

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

El sistema debe servir un front-end web a clientes PC y tablet, ofrecer un canal de suscripciones a noticias, gestionar el cobro de suscripciones con tres modalidades de facturación a través de Stripe, calcular cuatro métricas de uso para administradores y redactores, y persistir la información en dos bases de datos PostgreSQL 19 separadas. RD1 exige que las peticiones entre el front-end y el back-end se realicen mediante HTTP/REST.

El problema es elegir un estilo que organice el sistema en componentes con responsabilidades delimitadas, materialice la frontera HTTP/REST exigida y permita aislar los elementos externos (Stripe, PostgreSQL), sin imponer una complejidad de despliegue que un equipo de cinco personas no pueda modelar ni defender en tres semanas.

## Decision Drivers

* **RD1 — Comunicación mediante HTTP/REST:** las peticiones entre front-end y back-end deben realizarse mediante protocolos HTTP/REST, lo que exige una frontera explícita entre la interfaz y la lógica del sistema.

## Considered Options

* 0001-1 — Cliente-servidor en tres capas (presentación / negocio / persistencia) con front-end SPA y API REST.
* 0001-2 — Microservicios, un servicio por módulo funcional (noticias, suscripciones, pagos, estadísticas).
* 0001-3 — Monolito con MVC renderizado en servidor.
* 0001-4 — Monolito hexagonal (puertos y adaptadores) en un único despliegue.

## Decision Outcome

Chosen option: **0001-1 — Cliente-servidor en tres capas con SPA y API REST**, because es la única opción que materializa la frontera HTTP/REST de RD1 sin fragmentar el sistema en unidades de despliegue independientes que ningún requisito del Anexo exige.

### Pros and Cons of the Options

#### 0001-1 — Cliente-servidor en tres capas con SPA y API REST

* Good, because la capa de presentación queda aislada: el front-end responsivo se sirve como un cliente más de la API y puede adaptarse a PC o tablet sin tocar la lógica de suscripciones ni de facturación.
* Good, because la frontera HTTP/REST de RD1 se materializa en un único punto de entrada al back-end, de modo que el módulo de estadísticas y el de suscripciones consumen los mismos servicios sin duplicar lógica.
* Good, because la capa de persistencia queda separada de la de negocio, lo que permite tratar las dos bases de datos PostgreSQL 19 como dos adaptadores distintos sin que el dominio sepa cuál se está usando.
* Bad, because el front-end SPA obliga a duplicar en el cliente validaciones que también deben existir en el servidor, y a mantener un contrato de API que hay que versionar cuando cambien las operaciones de suscripción.

#### 0001-2 — Microservicios por módulo funcional

* Good, because el módulo de estadísticas tiene un perfil de carga distinto al de facturación, y separarlo permitiría escalarlo sin replicar el resto.
* Good, because aislar el servicio de pagos facilitaría sustituir Stripe sin afectar a noticias o suscripciones.
* Bad, because exigiría coordinar cuatro servicios, dos bases de datos y la pasarela de pago, y ningún requisito del Anexo impone escalado independiente que lo justifique.
* Bad, because el alta de una suscripción atraviesa suscripciones, pagos y estadísticas: en microservicios obligaría a transacciones distribuidas o compensaciones, cuando la activación depende del resultado del cobro y en un único despliegue se resuelve con una transacción local.

#### 0001-3 — Monolito con MVC renderizado en servidor

* Good, because es la opción con menos piezas: no hay contrato de API que mantener ni cliente separado que desplegar.
* Bad, because contradice RD1, que exige peticiones HTTP/REST y por tanto presupone un back-end que expone servicios consumibles, no solo páginas renderizadas.
* Bad, because las vistas responsivas quedarían mezcladas con la lógica de negocio en el mismo despliegue, lo que dificulta que el módulo de estadísticas se consuma desde una interfaz de administración distinta de la del lector.

#### 0001-4 — Monolito hexagonal con puertos y adaptadores

* Good, because aislaría Stripe y las dos bases de datos detrás de puertos, que es exactamente lo que necesitan la integración de pagos y la separación de la persistencia.
* Bad, because en esta fase obliga a definir puertos para funcionalidades aún no decididas, y el resultado sería un diagrama con más interfaces que clases de dominio.
* Bad, because el aislamiento que aporta se obtiene igualmente dentro de la capa de negocio de 0001-1, sin adoptar el estilo completo.

## Consequences

* Good, because el diagrama de paquetes se organiza en tres paquetes de nivel superior (presentación, negocio, persistencia), lo que deja un sitio evidente para cada decisión posterior.
* Good, because Stripe y las dos bases de datos quedan en la capa de persistencia e integración, de modo que las decisiones 0006 y 0009 no contaminan el dominio.
* Bad, because hay que mantener y documentar el contrato de la API REST, ya que front-end y back-end se diseñan por separado.
* Bad, because el módulo de estadísticas comparte despliegue con el resto del back-end y no puede escalarse de forma independiente.

## Links

* Requisitos: RD1.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0006 (integración de pagos), 0009 (acceso a bases de datos).

---

