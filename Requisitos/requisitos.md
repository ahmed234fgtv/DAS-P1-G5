# Requisitos del sistema

## Requisitos funcionales

| ID | Nombre | Descripción |
| --- | --- | --- |
| RF01 | Gestión de peticiones del periódico digital | El sistema deberá atender las peticiones de los clientes que accedan al periódico digital a través del front-end web. La comunicación entre el front-end y el back-end se realizará mediante HTTP/REST, y el back-end contará con dos bases de datos PostgreSQL 19 separadas: una para administradores y redactores y otra para suscriptores. |
| RF02 | Diferenciación de clientes | El sistema deberá ser capaz de distinguir a cada cliente que acceda al periódico digital. |
| RF03 | Suscripción a noticias | El sistema deberá permitir a los lectores suscribirse de forma anónima a noticias de las categorías política, economía, sociedad, nacionales e internacionales. |
| RF04 | Baja de suscripciones a noticias | El sistema deberá permitir a los suscriptores darse de baja de sus suscripciones a noticias de forma anónima. |
| RF05 | Gestión de modalidades de facturación | El sistema deberá gestionar tres modalidades de facturación: trimestral, con un pago recurrente de 70 € cada tres meses (descuento base); semestral, con un pago recurrente de 130 € cada seis meses (descuento intermedio); y anual, con un pago de 190 € cada doce meses (máximo ahorro por mes). |
| RF06 | Gestión de pagos online | El sistema deberá gestionar los pagos online de las suscripciones. |
| RF07 | Reintentos de pagos fallidos |El sistema deberá permitir reintentar una petición de pago fallida hasta un máximo de tres veces. |
| RF08 | Estadísticas de noticias más leídas | El sistema deberá proporcionar a los administradores y redactores estadísticas que permitan identificar las noticias más leídas. |
| RF09 | Estadísticas de tiempo de permanencia | El sistema deberá proporcionar a los administradores y redactores estadísticas sobre el tiempo de permanencia de los usuarios. |
| RF10 | Estadísticas de tasa de rebote | El sistema deberá proporcionar a los administradores y redactores estadísticas sobre las tasas de rebote. |
| RF11 | Estadísticas de conversión | El sistema deberá proporcionar a los administradores y redactores estadísticas sobre la conversión de lectores a suscriptores. |

## Requisitos de dominio

| ID | Nombre | Descripción |
| --- | --- | --- |
| RD1 | Comunicación HTTP/REST | Las peticiones del front-end web deberán realizarse mediante HTTP/REST. |
| RD2 | Pasarela de pago Stripe | Los pagos online deberán gestionarse mediante Stripe. |
| RD3 | Bases de datos PostgreSQL 19 | El back-end deberá incluir dos bases de datos PostgreSQL 19: una para administradores y redactores y otra para suscriptores. |
