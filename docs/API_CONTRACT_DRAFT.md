# Borrador de contrato API

**PROPUESTA:** todos los métodos, campos, ejemplos, políticas, códigos y permisos siguientes requieren aprobación; no existe implementación ni contrato definitivo. `PATCH/DELETE /assets/{id}` están incluidos solo para discusión: su alcance es **PENDIENTE** y no deben habilitarse hasta que se apruebe. Los IDs y nombres son ficticios, sin referencias a destinos reales.

## Convenciones compartidas propuestas

- JSON UTF-8; fecha UTC RFC 3339. IDs opacos de ejemplo (`a1`, `r1`, `f1`, `t1`, `u1`); tipo y formato definitivo **PENDIENTE**. `Accept: application/json`; para JSON con cuerpo `Content-Type: application/json`. Sin cuerpo en GET/DELETE. Consultas desconocidas o campos desconocidos: `400` o `422` según naturaleza; evitar mensajes que reflejen datos sensibles.
- Salvo login: `Authorization: Bearer <token>`; sesiones/token y expiración **PENDIENTE**. Permisos propuestos `asset:read`, `asset:create`, `asset:update`, `asset:delete`, `cpe:read`, `cve:check`, `scan:create`, `scan:read`, `finding:read`, `finding:update`, `dashboard:read`; asignación por roles, aislamiento por recurso y política de ocultación **PENDIENTE**. Comprobar también acceso a cada recurso; no confiar en IDs del cliente.
- Listas: `limit` entero 1–100 (defecto 20), `offset` entero ≥0 (defecto 0); respuesta `{"items":[],"page":{"limit":20,"offset":0,"total":0}}`. Orden estable por ID propuesto; cambios concurrentes pueden mover páginas. `total` solo si conocido; con NVD puede ser parcial y debe indicarse cobertura.
- Formato uniforme: `{"error":{"code":"VALIDATION_ERROR","message":"Solicitud inválida","request_id":"req1","fields":[{"field":"manufacturer","message":"Requerido"}]}}`. `fields` opcional; nunca incluir secretos, stack traces o cuerpos externos. `400` JSON/query inválida; `401` sin identidad; `403` sin permiso; `404` no encontrado/ocultado; `409` conflicto; `422` validación semántica; `429` cuota; `500` error interno; `502` proveedor inválido/no disponible; `503` servicio temporalmente indisponible; `504` timeout externo. Acordar semántica definitiva, especialmente 403/404 y reintentos.
- `POST /assets`, `/assets/{id}/cve-check` y `/scans`: `Idempotency-Key` opaca, generada y reutilizada por el cliente al reintentar el mismo cuerpo, única por actor y ruta. Transacción reserva clave y efecto; reintento idéntico devuelve la respuesta original, cuerpo distinto con misma clave `409`; solicitudes simultáneas se serializan o responden conflicto temporal, y retención de claves es **PENDIENTE**. Esto previene duplicados tras pérdida de respuesta; no supone sincronización offline. Definir qué ocurre tras expiración antes de desplegar.
- TLS, límites de cuerpo y tasa por usuario/operación, límites globales NVD, validación en servidor, protección contra abuso, registros sin datos sensibles y autorización por objeto son **PROPUESTA**. No confiar en que la clave idempotente sustituya validación o permisos.

## Endpoints propuestos

### 1. POST /auth/login

**Objetivo:** iniciar sesión. **Autenticación:** ninguna; proteger frente a enumeración y abuso. **Permiso:** ninguno antes de identificar. **Parámetros:** ninguno. **Headers:** JSON comunes. **Request:** `{"username":"analista","password":"<credencial-de-ejemplo>"}`. **Response 200:** `{"access_token":"<token-de-ejemplo>","token_type":"Bearer","expires_in":3600}`. Contraseña no real; no registrar cuerpo. Validar formato y longitud sin revelar si existe usuario. Errores: `400`, `401` genérico, `429`, `500`. La autenticación de VulnTrack no implica almacenar contraseñas de equipos ni secretos de clientes. Duración y mecanismo definitivos **PENDIENTE**.

### 2. GET /auth/me

**Objetivo:** identidad actual. **Autenticación:** bearer; **permiso:** sesión válida. **Parámetros:** ninguno; **headers:** comunes; **request:** sin cuerpo. **Response 200:** `{"id":"u1","username":"analista","permissions":["asset:read"]}`. Errores: `401`, `500`; filtrar información personal y no devolver secretos.

### 3. GET /assets

**Objetivo:** listado de activos visibles. **Autenticación:** bearer; **permiso:** `asset:read`. **Parámetros:** `limit`, `offset`; filtros aún **PENDIENTE**. **Headers:** comunes; **request:** sin cuerpo. **Response 200:** `{"items":[{"id":"a1","device_type":"router","manufacturer":"Fabricante ejemplo","model":"Modelo ejemplo","version":"1.2","site_label":"SITIO-1","inventory_label":null,"notes":null}],"page":{"limit":20,"offset":0,"total":1}}`. Validar paginación; limitar resultados al ámbito autorizado. Errores comunes `400`, `401`, `403`, `500`.

### 4. POST /assets

**Objetivo:** registrar activo; no escanearlo. **Autenticación:** bearer; **permiso:** `asset:create`. **Parámetros:** ninguno; **headers:** JSON comunes + `Idempotency-Key`. **Request:** `{"device_type":"router","manufacturer":"Fabricante ejemplo","model":"Modelo ejemplo","version":"1.2","site_label":"SITIO-1","inventory_label":null,"notes":null}`. **Response 201:** `{"id":"a1","device_type":"router","manufacturer":"Fabricante ejemplo","model":"Modelo ejemplo","version":"1.2","site_label":"SITIO-1","inventory_label":null,"notes":null}`; `Location: /assets/a1` propuesto. Validar tipo y campos no vacíos, longitud y observaciones no sensibles; listas y longitudes definitivas **PENDIENTE**. Errores `400`, `401`, `403`, `409`, `422`, `429`, `500`; no permitir asignar propietario/permisos mediante campos no reconocidos.

### 5. GET /assets/{id}

**Objetivo:** detalle de activo. **Autenticación:** bearer; **permiso:** `asset:read` sobre recurso. **Parámetros:** `id` opaco; **headers:** comunes; **request:** sin cuerpo. **Response 200:** mismo objeto de POST /assets. Validar ID y acceso; errores `400`, `401`, `403`/`404`, `500`. No revelar existencia a usuarios sin acceso cuando se apruebe política de ocultación.

### 6. PATCH /assets/{id}

**Objetivo:** eventual actualización parcial. **Autenticación:** bearer; **permiso:** `asset:update`; operación y campos **PENDIENTE**. **Parámetros:** ID; **headers:** comunes; propuesta `If-Match` si se adopta control de concurrencia. **Request hipotético:** `{"notes":"Revisión de inventario"}`. **Response hipotética 200:** mismo objeto de activo con `notes` actualizado. Validar únicamente campos permitidos cuando se aprueben, versión y permisos; sin reglas definitivas. Errores propuestos `400`, `401`, `403`/`404`, `409`, `412` si falla precondición, `422`. No exponer ni habilitar hasta definición.

### 7. DELETE /assets/{id}

**Objetivo:** eventual eliminación. **Autenticación:** bearer; **permiso:** `asset:delete`; alcance y política de borrado **PENDIENTE**. **Parámetros:** ID; **headers:** comunes; **request:** sin cuerpo; **response hipotética 204:** sin cuerpo. Validar autorización y dependencias históricas; decidir borrado lógico/físico, retención e idempotencia antes de habilitar. Errores `400`, `401`, `403`/`404`, `409` por dependencias. Ninguna regla de cascada está aprobada.

### 8. GET /cpe/search?q=

**Objetivo:** buscar candidatos CPE. **Autenticación:** bearer; **permiso:** `cpe:read`. **Parámetros:** `q` no vacío, `limit`, `offset`; **headers:** comunes; **request:** sin cuerpo. **Response 200:** `{"items":[{"cpe_name":"cpe:2.3:a:ejemplo:producto:1.2:*:*:*:*:*:*:*","title":"Producto de ejemplo 1.2","deprecated":false}],"page":{"limit":20,"offset":0,"total":1}}`. Ejemplo sintético, no afirma existencia en NVD. Validar texto y límites; encode al consultar NVD; no interpretar `q` como URL. Errores `400`, `401`, `403`, `429`, `502`, `504`. Selección es manual y puede quedar sin candidato.

### 9. POST /assets/{id}/cve-check

**Objetivo:** consulta a NVD para CPE seleccionado. **Autenticación:** bearer; **permiso:** `cve:check` y lectura del activo. **Parámetros:** ID; **headers:** JSON comunes + `Idempotency-Key`. **Request:** `{"cpe_name":"cpe:2.3:a:ejemplo:producto:1.2:*:*:*:*:*:*:*"}`. **Response 200:** `{"analysis_id":"r1","asset_id":"a1","source":"NVD","cpe_name":"cpe:2.3:a:ejemplo:producto:1.2:*:*:*:*:*:*:*","coverage":"complete","finding_count":1,"checked_at":"2026-10-30T12:00:00Z"}`. `complete` se refiere a páginas recuperadas, no a cobertura universal. Validar selección explícita, sintaxis, campos requeridos por NVD y autorización; limitar páginas y tiempo. Errores `400`, `401`, `403`/`404`, `409`, `422`, `429`, `502`, `504`. Definir comportamiento parcial y persistencia antes de aprobación; resultados son potenciales.

### 10. POST /scans

**Objetivo:** solicitar Baseline. **Autenticación:** bearer; **permiso:** `scan:create` sobre destino autorizado. **Parámetros:** ninguno; **headers:** JSON comunes + `Idempotency-Key`. **Request:** `{"target_id":"t1"}`; nunca URL, configuración libre o flags del usuario. **Response 202:** `{"id":"r2","kind":"zap_baseline","target_id":"t1","status":"queued"}`; `Location: /scans/r2`. Validar autorización vigente, lista y controles de salida antes de ejecutar; devolver fallo cerrado si no se garantizan. Errores `400`, `401`, `403`/`404`, `409`, `422`, `429`, `503`. La forma de administrar la lista está **PENDIENTE**; no se añade un endpoint implícito.

### 11. GET /scans/{id}

**Objetivo:** estado y resumen. **Autenticación:** bearer; **permiso:** `scan:read` sobre ejecución. **Parámetros:** ID; **headers:** comunes; **request:** sin cuerpo. **Response 200:** `{"id":"r2","kind":"zap_baseline","target_id":"t1","status":"completed","finding_count":1,"completed_at":"2026-10-30T12:05:00Z"}`. Estados propuestos `queued`, `running`, `completed`, `failed`; no equivalen a estados formales del proyecto. Validar ID y autorización; errores `400`, `401`, `403`/`404`, `500`. Código 1 o 2 de ZAP puede representar alertas y no fallo técnico.

### 12. GET /findings

**Objetivo:** listar hallazgos permitidos. **Autenticación:** bearer; **permiso:** `finding:read`. **Parámetros:** `limit`, `offset`, `analysis_id` opcional; **headers:** comunes; **request:** sin cuerpo. **Response 200:** `{"items":[{"id":"f1","analysis_id":"r1","source":"NVD","external_id":"CVE-2026-12345","applicability":"potential","status":"open","cvss":null}],"page":{"limit":20,"offset":0,"total":1}}`. Identificador CVE ficticio; ningún hallazgo real afirmado. `potential` aplica a NVD; una alerta ZAP debe conservar su fuente y no representar CVE automáticamente. Validar filtro y autorización por análisis; errores `400`, `401`, `403`, `500`.

### 13. PATCH /findings/{id}/status

**Objetivo:** proponer cambio de estado. **Autenticación:** bearer; **permiso:** `finding:update` sobre hallazgo. **Parámetros:** ID; **headers:** comunes, control de concurrencia por definir. **Request:** `{"status":"reviewed"}`. **Response 200:** `{"id":"f1","analysis_id":"r1","source":"NVD","external_id":"CVE-2026-12345","applicability":"potential","status":"reviewed","cvss":null}`. `reviewed` no implica aplicabilidad confirmada. Transiciones, evidencia, historial y quién verifica siguen **PENDIENTE**. Errores `400`, `401`, `403`/`404`, `409`, `422`. No permitir promover certeza automáticamente.

### 14. GET /dashboard/summary

**Objetivo:** resumen de información autorizada. **Autenticación:** bearer; **permiso:** `dashboard:read`. **Parámetros:** ninguno; filtros **PENDIENTE**; **headers:** comunes; **request:** sin cuerpo. **Response 200:** `{"assets_total":1,"potential_cve_findings":1,"scan_runs_total":1,"as_of":"2026-10-30T12:05:00Z"}`. Conteos de recursos accesibles, no únicos globales; definición de deduplicación, frescura y estado **PENDIENTE**. Errores `401`, `403`, `500`. No revelar totales fuera del alcance del usuario.
