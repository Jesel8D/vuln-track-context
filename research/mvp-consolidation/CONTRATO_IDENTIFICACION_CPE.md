# Contrato de identificación CPE

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Contrato y nomenclatura

`cpe_name` es el nombre CPE 2.3 devuelto por el diccionario; `cpe_name_id` su UUID. `matchCriteriaId` identifica un criterio publicado en configuración CVE y no identifica la selección local. La versión observada no se reescribe con la versión normalizada de NVD. Un `part=h` identifica hardware, `o` SO/firmware, `a` aplicación; ni el tipo router ni el nombre comercial bastan para elegirlo.

La búsqueda usa GET `/api/v1/cpe-candidates` con `query` (2–200 caracteres), `limit` (1–20, default 10), `offset` (0–10000) y opcional `product_query` (1–160). Para evitar dos términos de búsqueda superpuestos, cuando hay product_query el backend construye keywordSearch con query más product_query; la UI puede omitir product_query y enviar directamente una query tecnológica completa. Normalizar NFC/strip, sin controles, no transliterar ni inventar vendor/product; URL encoding al enviar `keywordSearch`, `resultsPerPage`, `startIndex`. No concatenar un CPE definitivo. No enviar sitio, inventario o notas. La búsqueda requiere autenticación, pero los candidatos son catálogo público; no hay recursos ajenos que consultar.

Cada candidato muestra `cpe_name`, `cpe_name_id`, título, deprecated y metadatos de consulta. Comparar part, vendor, product, version, update y plataforma; destacar discrepancias. Búsqueda vacía responde 200 items vacíos; fallos NVD son errores, no «sin coincidencia». El UUID se resuelve nuevamente mediante `cpeNameId` antes de confirmar; ambas cadenas deben corresponder al registro oficial y no estar deprecadas. Prohibir reemplazar un registro deprecado sin elección humana; sugerir nueva búsqueda. Si NVD no está disponible no se escribe confirmación.

## Estados y transiciones

| Estado anterior | Operación | Resultado permitido | Condición |
|---|---|---|---|
| sin identificación (`pending` virtual) | confirmar | confirmed | propietario activo, candidato válido, component_kind, versión mostrada, motivo, expected_current_id=null |
| pending / confirmed / needs_review | confirmar | confirmed (nuevo evento) | expected_current_id coincide; motivo, NVD revalidado y verificación explícita de usuario |
| confirmed | marcar revisión | needs_review (nuevo evento) | motivo obligatorio; copia referencia al candidato anterior, sin tratarlo como confirmación vigente |
| needs_review | marcar revisión | prohibido 409 INVALID_TRANSITION | evitar eventos redundantes |
| pending | marcar revisión | prohibido 409 INVALID_TRANSITION | no existe confirmación que revisar |
| cualquiera | borrar historial o reabrir fila antigua | prohibido | historial inmutable, no DELETE API |

`SIN_COINCIDENCIA` y «candidatos encontrados» son resultados de búsqueda fechados en UI, no estados persistidos de identificación: evitan confundir ausencia momentánea de catálogo con identidad del activo. No se crea fila pending; se representa cuando no hay evento actual. Una selección confirmed puede tener observed_version null: identidad revisada, evaluación específica bloqueada por versión insuficiente. No crea certeza de aplicabilidad.

## Historial y concurrencia

Tabla única `asset_cpe_identifications`: cada cambio crea una fila, cierra `valid_to` del evento actual y lo referencia con `replaces_id`. Una fila representa tanto identificación como historial; no añadir tabla de historial duplicada. Confirmaciones conservan UUID/nombre, component_kind, observed_version, actor/fecha, motivo, fuente y validated_at. Un evento needs_review copia esos datos del evento anterior pero su recorded_by/at corresponde al revisor, no a la confirmación anterior; ésta sigue en la fila referenciada. La UI no muestra recorded_at de un evento needs_review como nueva fecha de confirmación.

Bloquear fila assets en transacción corta; comprobar propietario y `expected_current_id`, cerrar actual e insertar evento de forma atómica. Un índice único parcial por asset_id con valid_to null impide dos actuales. Primer registro exige expected_current_id=null; dos confirmaciones distintas simultáneas: una gana y otra recibe 409 CURRENT_IDENTIFICATION_CHANGED. Idempotencia por actor/activo/clave: comprobar replay autorizado antes de expected_current_id; hash incluye cuerpo normalizado y ruta. Misma clave/cuerpo devuelve evento original; distinta huella 409 IDEMPOTENCY_CONFLICT. La respuesta de creación solo contiene campos inmutables para replay estable; consultas GET añaden valid_to e is_current calculado. No usar la tabla V1 cuya ruta está fijada a `/assets`.

Datos históricos permanecen ligados a sus análisis. Si cambia identificación, el dashboard marca análisis anterior como «de identificación previa», no reinterpreta hallazgos ni elimina evidencia. Un error humano se corrige con un evento nuevo y motivo.
