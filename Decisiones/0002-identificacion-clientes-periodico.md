# 0002 — Identificación de los clientes del periódico

* Status: proposed
* Deciders: ASS del Grupo 5
* Date: 2026-11-06

## Context and Problem Statement

RF01 exige que el sistema distinga a cada cliente que acceda al periódico digital. El Anexo no especifica con qué finalidad ni con qué mecanismo, y el sistema no dispone de cuentas de usuario: el lector accede sin registrarse. Sin embargo, las cuatro métricas de uso que se presentan a administradores y redactores solo tienen sentido si las lecturas se pueden atribuir a clientes distintos, de modo que una misma persona que abre tres noticias no cuente como tres lectores diferentes.

El problema es decidir qué grado de identificación se implanta, cuánto dura y dónde se persiste, sabiendo que cualquier mecanismo que exija datos personales choca con el carácter anónimo del acceso al periódico.

## Decision Drivers

* **RF01 — Diferenciación de clientes:** el sistema debe distinguir a cada cliente que acceda al periódico digital.

## Considered Options

* 0002-1 — Identificador de sesión anónimo generado por el back-end y transportado en cookie o token.
* 0002-2 — Autenticación de usuario con credenciales.
* 0002-3 — Identificación por huella del dispositivo (fingerprinting) sin estado en el servidor.
* 0002-4 — Identificador generado en el cliente y almacenado en el navegador.

## Decision Outcome

Chosen option: **0002-1 — Identificador de sesión anónimo generado por el back-end**, because distingue clientes sin exigir datos personales y sitúa la generación del identificador en el servidor, donde puede validarse y caducarse.

### Pros and Cons of the Options

#### 0002-1 — Identificador de sesión anónimo generado por el back-end

* Good, because cumple RF01 sin recoger ningún dato personal: el identificador es un valor opaco que no revela quién es el cliente.
* Good, because al generarlo el servidor, la caducidad y la rotación quedan bajo control del sistema, lo que evita que un cliente manipule su identificador para inflar las métricas de noticias más leídas.
* Good, because permite atribuir a un mismo cliente varias lecturas dentro de la sesión, que es la condición para calcular el tiempo de permanencia y la tasa de rebote sin contar cada visita como un lector nuevo.
* Bad, because un cliente que borre la cookie o cambie de dispositivo aparecerá como un cliente distinto, lo que introduce un error conocido en la tasa de conversión de lectores a suscriptores.
* Bad, because obliga a mantener estado de sesión en el back-end, con la consiguiente política de expiración que hay que definir y documentar.

#### 0002-2 — Autenticación de usuario con credenciales

* Good, because identificaría de forma fiable y persistente a cada cliente, eliminando el error por cambio de dispositivo.
* Bad, because exige registro con datos personales, lo que contradice el acceso anónimo al periódico y obligaría a gestionar credenciales, recuperación de contraseña y almacenamiento seguro.
* Bad, because añade una barrera de entrada al lector ocasional, justo el perfil que la tasa de conversión de lectores a suscriptores debe medir.

#### 0002-3 — Identificación por huella del dispositivo

* Good, because no requiere estado en el servidor ni almacenamiento en el cliente.
* Bad, because se apoya en características del dispositivo que pueden interpretarse como datos personales, lo que choca con el carácter anónimo del acceso.
* Bad, because es imprecisa y cambiante: dos tablets del mismo modelo pueden producir la misma huella, y una actualización del navegador la altera, lo que corrompe las métricas de conversión.

#### 0002-4 — Identificador generado en el cliente

* Good, because no consume recursos del servidor ni requiere estado de sesión.
* Bad, because el cliente controla el valor, de modo que puede reiniciarlo a voluntad y falsear el ranking de noticias más leídas.
* Bad, because no hay forma de validar su unicidad ni de caducarlo, y el identificador se pierde al limpiar el navegador.

## Consequences

* Good, because el identificador de sesión es la clave sobre la que se apoyan las métricas de 0008, sin necesidad de datos personales.
* Good, because la política de expiración queda centralizada en un único componente de la capa de negocio.
* Bad, because hay que decidir y documentar la duración de la sesión, ya que afecta directamente al cálculo del tiempo de permanencia y de la tasa de rebote.
* Bad, because el error por cambio de dispositivo debe declararse como limitación conocida de las métricas.

## Links

* Requisitos: RF01.
* Anexo I del enunciado, página 1.
* Decisiones relacionadas: 0008 (estadísticas de uso).

---

