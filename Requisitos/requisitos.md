# Requisitos del sistema

## Requisitos funcionales

| ID | Nombre | Descripción |
| --- | --- | --- |
| RF01 | Diferenciación de clientes | El sistema deberá ser capaz de distinguir a cada cliente que acceda al periódico digital. |
| RF02 | Suscripción a noticias | El sistema deberá permitir a los lectores suscribirse a diferentes noticias. |
| RF03 | Baja de suscripciones a noticias | El sistema deberá permitir a los suscriptores darse de baja de sus suscripciones a noticias. |
| RF04 | Gestión de modalidades de facturación | El sistema deberá gestionar los ciclos de facturación de las suscripciones mediante tres modalidades: trimestral, semestral y anual. |
| RF05 | Gestión de pagos online | El sistema deberá gestionar los pagos online de las suscripciones. |
| RF06 | Reintentos de pagos fallidos | El sistema deberá permitir reintentar una petición de pago cuando esta haya fallado. |
| RF07 | Estadísticas de noticias más leídas | El sistema deberá proporcionar a los administradores y redactores estadísticas que permitan identificar las noticias más leídas. |
| RF08 | Estadísticas de tiempo de permanencia | El sistema deberá proporcionar a los administradores y redactores estadísticas sobre el tiempo de permanencia de los usuarios. |
| RF09 | Estadísticas de tasa de rebote | El sistema deberá proporcionar a los administradores y redactores estadísticas sobre las tasas de rebote. |
| RF10 | Estadísticas de conversión | El sistema deberá proporcionar a los administradores y redactores estadísticas sobre la conversión de lectores a suscriptores. |

## Requisitos de dominio

| ID | Nombre | Descripción |
| --- | --- | --- |
| RD1 | Comunicación HTTP/REST | Las peticiones del front-end web deberán realizarse mediante HTTP/REST. |
| RD2 | Pasarela de pago Stripe | Los pagos online deberán gestionarse mediante Stripe. |
| RD3 | Bases de datos PostgreSQL 19 | El back-end deberá incluir dos bases de datos PostgreSQL 19: una para administradores y redactores y otra para suscriptores. |

## Nota pendiente de revisión con Rafa

Queda pendiente aclarar el límite de tres reintentos de pago deben detallarse dentro de las descripciones de sus requisitos funcionales o recogerse como requisitos de dominio. También queda pendiente la ubicación de las demás condiciones explícitas del Anexo, como las categorías de noticias y el anonimato de las suscripciones y sus bajas. Estas condiciones no se consideran eliminadas del alcance del sistema.
