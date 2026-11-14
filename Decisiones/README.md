# Decisiones arquitectónicas

Las decisiones se documentan siguiendo la plantilla [adr-template.md](./adr-template.md). Las versiones anteriores y sus anulaciones se conservan en esta carpeta para reflejar los cambios del diseño.

## Decisiones vigentes

1. [0001-4 — Selección del estilo arquitectónico del periódico digital](./0001-4-estilo-arquitectonico.md)
2. [0002-4 — Diferenciación de clientes y medición de uso para las estadísticas](./0002-4-diferenciacion-clientes-estadisticas.md)
3. [0003-4 — Gestión anónima de suscripciones a noticias y sus bajas](./0003-4-suscripciones-anonimas.md)
4. [0004-4 — Organización de la facturación y el cobro de suscripciones](./0004-4-facturacion-cobro.md)
5. [0005-4 — Organización del acceso a las dos bases de datos PostgreSQL 19](./0005-4-acceso-bases-datos.md)

Estas cinco decisiones están en estado `proposed` mientras no se cambie formalmente su estado.

## Reorganización de las decisiones anteriores

| Decisión anterior | Situación actual |
| --- | --- |
| 0001-2 | Sustituida por 0001-4; se conserva la elección de estilo. |
| 0002-2 | Incorporada a 0002-4 junto con las estadísticas de uso. |
| 0003-2 | Sustituida por 0003-4; se conserva el mecanismo de suscripción y baja anónimas. |
| 0004-2 | Integrada en 0004-4 junto con pagos y reintentos. |
| 0005-2 | Integrada en 0004-4: pagos mediante Stripe. |
| 0006-2 | Integrada en 0004-4: control de reintentos. |
| 0007-2 | Integrada en 0002-4: estadísticas y diferenciación de clientes. |
| 0008-2 | Reorganizada como 0005-4: acceso a las bases de datos PostgreSQL 19. |

Las anulaciones se registran en los archivos `0001-3` a `0008-3`, uno por decisión. Los documentos originales y sus versiones `-2` no se han borrado ni modificado.

La numeración `0005-4` identifica ahora la decisión de acceso a las bases de datos. No debe confundirse con la antigua decisión `0005` sobre Stripe, cuyo contenido queda integrado en `0004-4`.
