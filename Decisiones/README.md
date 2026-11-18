# Decisiones arquitectónicas

Las decisiones se documentan siguiendo la plantilla [adr-template.md](./adr-template.md). Las versiones anteriores y sus anulaciones se conservan en esta carpeta para reflejar los cambios del diseño.

## Decisiones vigentes

1. [0001-6 — Selección del estilo arquitectónico del periódico digital](./0001-6%20%E2%80%94%20Selecci%C3%B3n%20del%20estilo%20arquitect%C3%B3nico%20del%20peri%C3%B3dico%20digital.md)
2. [0002-6 — Diferenciación de clientes y medición de uso para las estadísticas](./0002-6%20%E2%80%94%20Diferenciaci%C3%B3n%20de%20clientes%20y%20medici%C3%B3n%20de%20uso%20para%20las%20estad%C3%ADsticas.md)
3. [0003-6 — Gestión anónima de las suscripciones a noticias y sus bajas](./0003-6%20%E2%80%94%20Gesti%C3%B3n%20an%C3%B3nima%20de%20las%20suscripciones%20a%20noticias%20y%20sus%20bajas.md)
4. [0004-6 — Organización de la facturación y el cobro de suscripciones](./0004-6%20%E2%80%94%20Organizaci%C3%B3n%20de%20la%20facturaci%C3%B3n%20y%20el%20cobro%20de%20suscripciones.md)
5. [0005-6 — Organización del acceso a las dos bases de datos PostgreSQL 19](./0005-6%20%E2%80%94%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md)

Estas cinco decisiones están en estado `proposed` mientras no se cambie formalmente su estado.

## Sustitución de las versiones `-4`

| Versión anterior | Anulación | Versión vigente |
| --- | --- | --- |
| [0001-4](./0001-4-estilo-arquitectonico.md) | [0001-5](./0001-5%20Anulamos%20Selecci%C3%B3n%20del%20estilo%20arquitect%C3%B3nico%20del%20peri%C3%B3dico%20digital.md) | [0001-6](./0001-6%20%E2%80%94%20Selecci%C3%B3n%20del%20estilo%20arquitect%C3%B3nico%20del%20peri%C3%B3dico%20digital.md) |
| [0002-4](./0002-4-diferenciacion-clientes-estadisticas.md) | [0002-5](./0002-5%20Anulamos%20Diferenciaci%C3%B3n%20de%20clientes%20y%20medici%C3%B3n%20de%20uso%20para%20las%20estad%C3%ADsticas.md) | [0002-6](./0002-6%20%E2%80%94%20Diferenciaci%C3%B3n%20de%20clientes%20y%20medici%C3%B3n%20de%20uso%20para%20las%20estad%C3%ADsticas.md) |
| [0003-4](./0003-4-suscripciones-anonimas.md) | [0003-5](./0003-5%20Anulamos%20Gesti%C3%B3n%20an%C3%B3nima%20de%20las%20suscripciones%20a%20noticias%20y%20sus%20bajas.md) | [0003-6](./0003-6%20%E2%80%94%20Gesti%C3%B3n%20an%C3%B3nima%20de%20las%20suscripciones%20a%20noticias%20y%20sus%20bajas.md) |
| [0004-4](./0004-4-facturacion-cobro.md) | [0004-5](./0004-5%20Anulamos%20Organizaci%C3%B3n%20de%20la%20facturaci%C3%B3n%20y%20el%20cobro%20de%20suscripciones.md) | [0004-6](./0004-6%20%E2%80%94%20Organizaci%C3%B3n%20de%20la%20facturaci%C3%B3n%20y%20el%20cobro%20de%20suscripciones.md) |
| [0005-4](./0005-4-acceso-bases-datos.md) | [0005-5](./0005-5%20Anulamos%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md) | [0005-6](./0005-6%20%E2%80%94%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md) |

## Historial de las versiones `-2`

| Decisión anterior | Reorganización anterior |
| --- | --- |
| 0001-2 | Sustituida por 0001-4; se conserva la elección de estilo. |
| 0002-2 | Incorporada a 0002-4 junto con las estadísticas de uso. |
| 0003-2 | Sustituida por 0003-4; se conserva el mecanismo de suscripción y baja anónimas. |
| 0004-2 | Integrada en 0004-4 junto con pagos y reintentos. |
| 0005-2 | Integrada en 0004-4: pagos mediante Stripe. |
| 0006-2 | Integrada en 0004-4: control de reintentos. |
| 0007-2 | Integrada en 0002-4: estadísticas y diferenciación de clientes. |
| 0008-2 | Reorganizada como 0005-4: acceso a las bases de datos PostgreSQL 19. |

Las anulaciones de estas versiones se registran en los archivos `0001-3` a `0008-3`. Los documentos originales y las versiones `-2` y `-4` se conservan para poder seguir la evolución de las decisiones.

La decisión vigente sobre acceso a las bases de datos es [0005-6](./0005-6%20%E2%80%94%20Organizaci%C3%B3n%20del%20acceso%20a%20las%20dos%20bases%20de%20datos%20PostgreSQL%2019.md). La antigua decisión `0005` trataba sobre Stripe, cuya integración se recoge en [0004-6](./0004-6%20%E2%80%94%20Organizaci%C3%B3n%20de%20la%20facturaci%C3%B3n%20y%20el%20cobro%20de%20suscripciones.md).
