# 0002-4 — Diferenciación de clientes y medición de uso para las estadísticas

* Status: proposed
* Date: 2026-11-14

## Context and Problem Statement

El sistema debe distinguir a cada cliente que acceda al periódico digital (RF02) y, además, administradores y redactores deben poder consultar noticias más leídas, tiempo de permanencia, tasa de rebote y conversión de lectores a suscriptores (RF08 a RF11). El Anexo I no aclara si «cliente» se refiere a una persona, un dispositivo, un navegador o una sesión, no exige registrar lectores con datos personales y tampoco fija fórmulas, umbrales ni define con precisión qué se considera lectura, visita o conversión.

Ambas cuestiones están relacionadas: para analizar visitas, permanencia o rebote es útil poder agrupar peticiones del mismo origen, aunque el recuento de lecturas por noticia no dependa necesariamente de una sesión. Un identificador por sí solo tampoco permite medir tiempos ni conocer las altas de suscripción. Se compara el modo de seguir la actividad anónima que alimenta las estadísticas y de distinguir las peticiones asociadas a ese seguimiento. No se añade un servicio externo de analítica, una tercera base de datos ni un registro de usuarios obligatorio.

## Decision Drivers

* **RF02 — Diferenciación de clientes:** el sistema debe ser capaz de distinguir a cada cliente que acceda al periódico digital.
* **RF08 — Estadísticas de noticias más leídas:** administradores y redactores deben poder identificar las noticias más leídas.
* **RF09 — Estadísticas de tiempo de permanencia:** deben poder consultar el tiempo de permanencia de los usuarios.
* **RF10 — Estadísticas de tasa de rebote:** deben poder consultar las tasas de rebote.
* **RF11 — Estadísticas de conversión:** deben poder consultar la conversión de lectores a suscriptores.

## Considered Options

* **Opción A — Sesión emitida por el back-end y registro de interacciones calculado al consultar.** El servidor entrega un identificador opaco que el navegador devuelve en sus peticiones posteriores mientras la sesión siga vigente. Se guardan hechos de uso, como apertura de noticia, referencia de sesión y momentos de interacción, y un servicio de estadísticas calcula las métricas a partir de ellos.
* **Opción B — Identificador persistente del navegador y acumulados actualizados durante la navegación.** El navegador crea y conserva localmente un identificador propio que envía con sus peticiones, y cada interacción actualiza contadores y duraciones agregadas que el módulo de estadísticas consulta directamente.
* **Opción C — Sesión emitida por el back-end y métricas acumuladas.** El servidor distingue las peticiones mediante sesiones, pero el sistema mantiene contadores y duraciones agregadas, en lugar de conservar los registros de cada interacción para las consultas posteriores.

## Decision Outcome

Chosen option: "Opción A — Sesión emitida por el back-end y registro de interacciones calculado al consultar", because el servidor controla la emisión y la caducidad de los identificadores sin exigir una cuenta de lector y el registro de hechos de uso permite revisar los cálculos si se concretan las definiciones de visita, permanencia y conversión. La opción C comparte el mecanismo de sesión, pero pierde parte de esa capacidad de revisión al conservar solo agregados. Un registro de interacción es un dato del sistema, no una decisión de implantar mensajería ni de recopilar datos personales.

### Consequences

* Good, because se pueden asociar varias peticiones a una misma sesión sin pedir nombre, correo ni contraseña.
* Good, because pueden calcularse las noticias más leídas y agruparse interacciones por sesión para obtener permanencia y rebote, y el hecho de alta de suscripción permite calcular conversiones sin identificar nominalmente al lector.
* Bad, because una misma persona puede tener sesiones diferentes si cambia de navegador o dispositivo o si caduca el identificador, de modo que una sesión no equivale a una persona única.
* Bad, because registrar interacciones individuales ocupa más espacio, exige acotar la información conservada y puede resultar más costoso que consultar valores acumulados.
* Bad, because debe definirse cómo se transmite y protege el identificador para impedir que una sesión sea suplantada.

### Confirmation

El UML debe situar la responsabilidad de crear y comprobar identificadores de sesión dentro de la entrada o de un componente de apoyo del back-end, y distinguir las responsabilidades de registrar información de uso y de proporcionar estadísticas a administradores y redactores. La representación de una interacción debe permitir conocer la noticia consultada, una referencia de sesión y datos de tiempo suficientes para evaluar la permanencia; la conversión requiere acceso al hecho de suscripción. Deben quedar reflejadas las cuatro métricas sin presentar como obligatorios valores o fórmulas que no aparecen en los requisitos. Se podrá incluir una clase representativa de sesión, sin relaciones entre clases. No se añadirá un registro de usuarios obligatorio, ni un servicio externo de analítica, ni una tercera base de datos, y no se decidirá aquí la ubicación de datos que el Anexo no asigna a una de las dos bases. La duración exacta de una sesión queda abierta, ya que el Anexo no la fija.

## Pros and Cons of the Options

### Opción A — Sesión emitida por el back-end y registro de interacciones calculado al consultar

* Good, because el servidor puede rechazar identificadores no emitidos por él, gestionar cuándo dejan de ser válidos y diferenciar peticiones sin ampliar el requisito con credenciales personales.
* Good, because puede rehacerse el ranking de noticias y recalcular las métricas al precisar su definición operativa, concentrando en un módulo la obtención de resultados para administradores y redactores.
* Bad, because cerrar el navegador, borrar sus datos o usar otra tablet puede generar otra sesión para el mismo lector, y exige una política de caducidad y un tratamiento cuidadoso del identificador.
* Bad, because mantiene más registros que una solución basada solo en agregados y, para medir permanencia en la última página de una visita, puede faltar un evento de salida que debe tratarse al concretar la medición.

### Opción B — Identificador persistente del navegador y acumulados actualizados durante la navegación

* Good, because permite reconocer de nuevo al mismo navegador después de una visita sin pedir un alta y el identificador puede incluirse desde la primera petición, sin que el servidor tenga que emitirlo antes.
* Good, because la consulta de los resultados estadísticos puede ser rápida porque los totales ya están calculados, y puede evitar conservar el detalle de todas las interacciones.
* Bad, because el navegador puede modificar o regenerar el identificador, de modo que el servidor no puede garantizar su unicidad, y su persistencia prolongada afecta al tratamiento de la actividad del lector.
* Bad, because si cambia la forma de contar visitas o rebotes los acumulados antiguos pueden no ser reconstruibles, y una actualización incorrecta en tiempo de navegación afecta a estadísticas posteriores sin disponer necesariamente del detalle para corregirla.

### Opción C — Sesión emitida por el back-end y métricas acumuladas

* Good, because el servidor controla los identificadores utilizados para agrupar las visitas sin exigir una cuenta personal.
* Good, because las estadísticas pueden consultarse sobre acumulados ya preparados, sin recorrer el detalle de todas las interacciones.
* Bad, because si cambia la definición de rebote o permanencia, los valores antiguos pueden no poder reconstruirse al no conservar los hechos originales.
* Bad, because necesita actualizar correctamente los contadores durante la navegación; los errores acumulados no son fáciles de identificar después.

## Links

* Requisito(s) motivador(es): RF02, RF08, RF09, RF10, RF11 — [tabla de requisitos](https://github.com/ahmed234fgtv/DAS-P1-G5/blob/main/Requisitos/requisitos.md).
* Fuente: Anexo I y enunciado de la Práctica 1, curso 2026-2027.
* Versión: sustituye a [MADR 0002-2](./0002-2-diferenciacion-clientes.md) e incorpora la responsabilidad de estadísticas que antes se trataba en [MADR 0007-2](./0007-2-estadisticas-uso.md). Los documentos anteriores se conservan como historial; las anulaciones originales constan en 0002-1 y 0007-1.
* Decisiones relacionadas: MADR 0003-4, cuyas altas de suscripción aportan el hecho necesario para la conversión sin incorporar aquí sus drivers.
