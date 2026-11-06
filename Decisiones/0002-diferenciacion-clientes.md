# 0002 — Mecanismo de diferenciación de clientes

* Status: proposed
* Date: 2026-11-06

## Context and Problem Statement

El Anexo I exige que el sistema pueda discriminar a cada cliente que realiza peticiones desde el front-end web. No precisa si «cliente» significa navegador, dispositivo, sesión o persona, ni establece un registro obligatorio de usuarios. Esta ambigüedad debe quedar visible: **no podemos afirmar que el sistema identifica personas de forma persistente**.

Para representar un mecanismo concreto en UML, hay que decidir qué identificador maneja el back-end para distinguir las interacciones de los clientes sin imponer registro previo. La adaptabilidad de la interfaz a PC y tablet es una condición distinta y no resuelve por sí misma la diferenciación entre clientes.

## Decision Drivers

* **RF01 — Diferenciación de clientes:** el sistema debe ser capaz de distinguir a cada cliente que acceda al periódico digital.

## Considered Options

* **0002-1 — Identificador de sesión opaco emitido por el servidor.** En el primer contacto, el back-end genera un identificador que el cliente devuelve en peticiones posteriores, mientras dura la sesión.
* **0002-2 — Identificador persistente generado por el navegador.** El cliente crea y conserva un identificador local que adjunta a las peticiones, y el servidor lo utiliza para diferenciar el origen.

Ambas opciones permiten distinguir interacciones del cliente sin exigir cuentas nominales, pero difieren en quién genera el identificador y durante cuánto tiempo puede vincularse la actividad.

## Decision Outcome

Chosen option: "0002-1 — Identificador de sesión opaco emitido por el servidor", because da al back-end control sobre la emisión y caducidad del identificador, evita que el cliente tenga que inventar y administrar su propia identidad persistente y no presupone registros de usuario que el Anexo no solicita.

Se elige **identificación de sesión**, no identificación inequívoca de una persona. Si Rafa interpreta «discriminar cada cliente» como identificar de forma estable a una persona o dispositivo, habrá que revisar esta decisión.

### Consequences

* Good, because la misma sesión puede distinguir peticiones sucesivas sin exigir que el lector facilite nombre, teléfono o credenciales.
* Bad, because una misma persona puede generar varios identificadores por cambio de navegador, dispositivo o expiración, por lo que esta decisión no demuestra identidad personal estable.
* **Pendiente de Rafa:** qué significa exactamente «discriminar cada cliente» si se exige una identificación distinta a la sesión.

### Confirmation

El UML deberá mostrar la **responsabilidad de emitir y validar un identificador de sesión** en la entrada del back-end, así como su uso al atender peticiones del cliente web. No debe aparecer como requisito de esta decisión una cuenta personal, un registro obligatorio o una relación que identifique inequívocamente a una persona. La duración concreta de la sesión queda sin fijar por el Anexo.

## Pros and Cons of the Options

### 0002-1 — Identificador de sesión opaco emitido por el servidor

* Good, because el mismo cliente puede presentar el identificador en varias peticiones HTTP/REST y el servidor puede relacionarlas sin pedir nombre, teléfono o correo.
* Good, because el servidor puede imponer la caducidad del identificador y rechazar formatos no emitidos por él, reduciendo el riesgo de identificadores arbitrarios.
* Bad, because borrar los datos de sesión, reiniciar el navegador o cambiar de dispositivo puede generar otro identificador para la misma persona; no permite deducir un número exacto de usuarios únicos.
* Bad, because requiere definir una duración de sesión y un modo de transmisión seguros; un identificador pseudónimo no está automáticamente exento de consideraciones de privacidad.

### 0002-2 — Identificador persistente generado por el navegador

* Good, because puede mantenerse entre visitas del mismo navegador sin un alta de usuario y simplifica la emisión desde el servidor.
* Good, because la primera petición ya puede llevar el identificador, sin necesitar que el back-end lo haya entregado anteriormente.
* Bad, because el cliente puede regenerarlo y multiplicar artificialmente los clientes aparentes; el servidor no controla el origen ni garantiza la unicidad.
* Bad, because persiste más allá de una sesión y requiere justificar su duración y tratamiento; además, sigue sin identificar una misma persona cuando utiliza dos dispositivos.

## Links

* Requisito motivador: RF01 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md)
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
