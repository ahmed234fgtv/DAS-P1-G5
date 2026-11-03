# Requisitos del sistema

## Requisitos funcionales

| Identificador | Nombre | Descripción |
| --- | --- | --- |
| RF01 | Diferenciación del tipo de cliente | El sistema deberá ser capaz de distinguir individualmente a cada cliente que acceda al periódico digital. |
| RF02 | Suscripción a noticias | El sistema deberá permitir a los lectores suscribirse a diferentes noticias. |
| RF03 | Baja de suscripciones a noticias | El sistema deberá permitir a los suscriptores darse de baja de sus suscripciones a noticias. |
| RF04 | Gestión de modalidades de facturación | El sistema deberá gestionar tres modalidades de facturación: trimestral, semestral y anual. |
| RF05 | Gestión de pagos online | El sistema deberá gestionar los pagos online correspondientes a las modalidades de facturación establecidas. |
| RF06 | Reintentos de pagos fallidos | El sistema deberá permitir reintentar una petición de pago cuando esta haya fallado. |
| RF07 | Estadísticas de noticias más leídas | El sistema deberá proporcionar estadísticas que permitan identificar las noticias más leídas. |
| RF08 | Estadísticas de tiempo de permanencia | El sistema deberá proporcionar estadísticas sobre el tiempo de permanencia de los usuarios. |
| RF09 | Estadísticas de tasa de rebote | El sistema deberá proporcionar estadísticas sobre las tasas de rebote. |
| RF10 | Estadísticas de conversión | El sistema deberá proporcionar estadísticas sobre la conversión de lectores a suscriptores. |

## Requisitos de dominio

| Identificador | Nombre | Descripción |
| --- | --- | --- |
| RD1 | Categorías de noticias | Las noticias podrán pertenecer a las siguientes categorías: política, economía, sociedad, nacionales o internacionales. |
| RD2.1 | Facturación trimestral | La modalidad trimestral contempla un pago recurrente de 70 € cada tres meses, con descuento base. |
| RD2.2 | Facturación semestral | La modalidad semestral contempla un pago recurrente de 130 € cada seis meses, con descuento intermedio. |
| RD2.3 | Facturación anual | La modalidad anual contempla un pago único recurrente de 190 € cada doce meses, con el máximo ahorro por mes. |
| RD3 | Anonimato de las suscripciones | La suscripción a noticias y la baja de dichas suscripciones podrán realizarse de manera anónima. |
| RD4 | Límite de reintentos de pago | Ante una petición de pago fallida, se permitirá un máximo de tres reintentos. |
| RD5 | Destinatarios de las estadísticas | Las estadísticas de uso del periódico digital estarán destinadas a administradores y redactores. |
