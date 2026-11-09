# Contrato API del MVP

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Convenciones y rutas canónicas

Todas las rutas externas usan `/api/v1`; los endpoints de salud existentes son GET `/health` y GET `/ready`. Login no requiere token; el resto requiere JWT válido y usuario activo. Nuevas operaciones de datos exigen propietario del activo; identidad desde JWT, nunca supplied owner. 404 para recurso ajeno/inexistente/sin propietario, antes de acceder a NVD. Variables de ruta UUID. Extras JSON prohibidos; fechas UTC ISO8601; longitudes y normalización del formulario. Error común conserva `{error:{code,message,request_id}}`; 401 con WWW-Authenticate Bearer, 429 con Retry-After; no transmitir errores externos en bruto.

| Estado | Método | Ruta canónica | Contrato mínimo |
|---|---|---|---|
| EXISTENTE | POST | /api/v1/auth/login | Login: username ASCII 1–80 y password 1–256; Token access_token/token_type/expires_in; 200,401,422,429; no Bearer |
| EXISTENTE | GET | /api/v1/auth/me | Bearer; UserRead id/username; 200,401 |
| CAMBIO PROPUESTO | POST | /api/v1/assets | Bearer; AssetCreate con version nullable tras migración; Idempotency-Key UUID; 201 AssetRead y Location; 401,409,413,422 |
| CAMBIO PROPUESTO | GET | /api/v1/assets | Bearer; limit 1–100 default20, offset 0–1000000; items/page; admitir version null en lectura |
| CAMBIO PROPUESTO | GET | /api/v1/assets/{asset_id} | Bearer y propietario; AssetRead; 200,401,404,422; admitir version null |
| NUEVO PROPUESTO | GET | /api/v1/cpe-candidates | Bearer; query, product_query opcional, limit 1–20 default10, offset 0–10000; 200 items/page/source_timestamp; 401,422,429,502,503,504 |
| NUEVO PROPUESTO | POST | /api/v1/assets/{asset_id}/cpe-identifications | Bearer y propietario; body discriminado, Idempotency-Key; 201 evento inmutable, Location; 401,404,409,413,422,429,502,503,504 |
| NUEVO PROPUESTO | GET | /api/v1/assets/{asset_id}/cpe-identifications | Bearer y propietario; scope=current|history defaultcurrent, limit 1–100 default20, offset 0–1000000; 200 current_state/items/page; 401,404,422 |
| NUEVO PROPUESTO | POST | /api/v1/assets/{asset_id}/analyses | Bearer y propietario; identification_id UUID, Idempotency-Key UUID; 202 id/accepted_at y Location; 401,404,409,413,422,429,503 |
| NUEVO PROPUESTO | GET | /api/v1/assets/{asset_id}/analyses | Bearer y propietario; limit/offset como listado actual; 200 items/page; 401,404,422 |
| NUEVO PROPUESTO | GET | /api/v1/assets/{asset_id}/analyses/{run_id} | Bearer y propietario, run pertenece a asset; 200 estado/coverage/conteos/avisos; 401,404,422 |
| NUEVO PROPUESTO | GET | /api/v1/assets/{asset_id}/analyses/{run_id}/findings | Bearer y propietario de toda la cadena; limit/offset; 200 items/page/run_status/coverage; 401,404,422 |
| NUEVO PROPUESTO | PATCH | /api/v1/assets/{asset_id}/analyses/{run_id}/findings/{finding_id} | Bearer y propietario de toda la cadena; revisión única y expected_updated_at; 200 hallazgo; 401,404,409,413,422 |

No diseñar aliases `/cpe/search`, `/cve-check`, `cpe-selection` o `selection_id` en el nuevo contrato: son propuestas antecedentes, no rutas implementadas. Campo local único: `identification_id`. Esta convergencia requiere DEC-10/DEC-01, no altera documentos previos. PATCH/DELETE de activos, refresh/logout, dashboard-summary especializado, workspaces y escaneos son POSTERIOR; dashboard mínimo utiliza listados existentes y nuevos sin sumar ejecuciones como si fueran CVE únicos.

## JSON propuesto por operación nueva

Ejemplos ilustrativos salvo nombre/UUID NVD proveniente de consulta histórica. UUID de activos/eventos/ejecuciones y fechas locales de ejemplos son ficticios. No se ejecutó ningún endpoint nuevo.

**Candidatos:** GET con `query=Cisco RV340 firmware`. Respuesta abreviada de estructura propuesta:
```json
{"items":[{"cpe_name_id":"C817E5BA-62C1-41E7-A15C-FB851FEA761E","cpe_name":"cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*","title":"Cisco Small Business RV340 Firmware 1.0.0.33","deprecated":false}],"page":{"limit":10,"offset":0,"total":1},"source_timestamp":"2026-11-06T06:30:00Z"}
```
El total 1 de este JSON es simulado para explicar estructura, no el resultado histórico de esa búsqueda. source_timestamp identifica la respuesta de origen, no confirmación del activo.

**Confirmación:** POST al recurso cpe-identifications, cuerpo:
```json
{"state":"confirmed","cpe_name_id":"C817E5BA-62C1-41E7-A15C-FB851FEA761E","component_kind":"firmware","expected_current_id":null,"reason":"Comparado con los datos observados del equipo"}
```
respuesta 201 estable (forma abreviada):
```json
{"id":"11111111-1111-4111-8111-111111111111","state":"confirmed","cpe_name_id":"C817E5BA-62C1-41E7-A15C-FB851FEA761E","cpe_name":"cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*","recorded_at":"2026-11-06T06:30:00Z"}
```
Respuesta completa añade asset_id, component_kind, observed_version, source, validated_at, recorded_by, reason y replaces_id (igual a expected_current_id aceptado); no incluye valid_to ni is_current, que pueden cambiar. Marcar revisión usa el mismo POST: `{ "state":"needs_review", "expected_current_id":"11111111-1111-4111-8111-111111111111", "reason":"El candidato ya no coincide" }`; otros campos se copian del anterior. confirmed exige cpe_name_id y component_kind; needs_review prohíbe ambos como input. expected_current_id y reason son siempre requeridos.

**Lectura actual/historia:** GET scope=current devuelve `{current_state:"pending",items:[],page:{limit:20,offset:0,total:0}}` sin identificación. Si existe, items contiene la fila actual con valid_to=null e is_current=true. scope=history ordena recorded_at/id ascendente e incluye todas las filas, con valid_to e is_current calculados; current_state informa el evento actual. No mezclar significado de total entre scope actual e histórico.

**Solicitar CVE:** POST analyses `{ "identification_id":"11111111-1111-4111-8111-111111111111" }`. Respuesta 202 `{ "id":"22222222-2222-4222-8222-222222222222", "accepted_at":"2026-11-06T06:31:00Z" }`, Location del GET de ejecución. accepted_at es created_at; el replay no miente sobre estado actual porque no incluye status mutable. GET listado devuelve `{items:[<resumen de ejecución>],page:{limit,offset,total}}`; GET detalle incluye identificación/CPE congelados y:
```json
{"id":"22222222-2222-4222-8222-222222222222","status":"completed","coverage":"partial","total_results":5,"retrieved_count":2,"stored_count":2,"warning_codes":["PAGE_BUDGET_REACHED"]}
```
Forma abreviada: detalle completo añade asset_id, identification_id, selected_cpe, observed_version, initiated_by, created_at, started_at, completed_at, source_timestamp y failure_code. Estas fechas/estados son ejemplo simulado, no ejecución histórica almacenada.

**Hallazgos:** GET devuelve `{items:[{id,external_id,source,title,applicability,status,cvss,evidence,updated_at}],page:{limit,offset,total},run_status,coverage}`. JSON simbólico de estructura, no JSON ejecutable con valores NVD inventados. evidence solo expone datos públicos necesarios. No derivar un conteo global de vulnerabilidades sumando CVE repetidos de varios análisis. Mostrar por defecto resultados de la última ejecución seleccionada con su alcance, incluso si hubo consulta parcial.

**Revisión:** PATCH cuerpo ilustrativo:
```json
{"status":"reviewed","applicability":"not_applicable","expected_updated_at":"2026-11-06T06:32:00Z","reason":"La plataforma requerida no está presente","evidence":"Revisión documental del componente principal"}
```
Respuesta 200 devuelve hallazgo con reviewed_by, reviewed_at, review_reason y review_evidence. No existe valor confirmed. potential/undetermined/not_applicable permitidos con motivo y evidencia; exactamente una revisión en MVP. Fecha esperada distinta o ya reviewed: 409 REVIEW_CONFLICT; tras respuesta perdida, cliente reconcilia con GET, no reintenta ciegamente PATCH.

## Idempotencia y límites propuestos

Registro mantiene política actual 24 h y hash v1; al introducir version null conservar hash de solicitudes antiguas y probar replay de snapshots previos, sin recálculo masivo. Confirmación y análisis guardan clave y huella en su fila, no usan asset_registration_requests; claves conservadas durante retención del proyecto, mismo actor/activo/operación. Autorización siempre antes de replay; misma clave distinto hash 409; replay devuelve mismo código y recurso. Mutex transaccional y UNIQUE resuelven carreras. Post de análisis bloquea segunda consulta activa del activo con 409 ANALYSIS_IN_PROGRESS; misma clave se resuelve primero como replay.

Límites propuestos: candidatos 6 solicitudes/minuto/usuario, confirmaciones 6/minuto/usuario, nuevas ejecuciones 1/minuto/activo y una activa por activo. Nunca confundirlos con cuota NVD oficial. Limitar presupuesto global y verificación de cuota antes de habilitar integración; 429 no es fallo definitivo. CORS actual permite GET/POST; PATCH nuevo requiere adaptar allow-methods y preflight más tests; headers de idempotencia actuales ya contemplados. Todos los nuevos métodos conservan cuerpo máximo 8192, no-store y request_id; no se añade If-Match para evitar cambio de headers, se usa expected_updated_at.
