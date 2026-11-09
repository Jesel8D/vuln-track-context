# Contrato API: propuesta, no implementada

Todos los endpoints requieren Bearer y autorización por propietario salvo la búsqueda pública NVD controlada a través del backend. `GET /api/v1/assets`, `GET /api/v1/assets/{id}` y `POST /api/v1/assets` ya existen. POST actual requiere `Idempotency-Key` UUID y responde 201, Location y AssetRead. El contrato actual no acepta campos nuevos.

| Método y ruta | Uso propuesto | Entrada / respuesta | Errores |
|---|---|---|---|
| GET `/api/v1/cpe-candidates?query=...&start_index=0` | candidato público para usuario autenticado | página `{items, start_index, total, source_timestamp}` | 422, 429 local, 502/503/504 |
| POST `/api/v1/assets/{id}/cpe-selection` | confirmar selección para activo propio | `{ "cpe_name_id":"UUID devuelto por NVD" }` → `{id,cpe_name,cpe_name_id,confirmed_at}` | 401,404,409,422,502 |
| POST `/api/v1/assets/{id}/analyses` | iniciar evaluación | `{ "selection_id":"UUID" }` → 202 `{id,status:"queued"}` | 401,404,409,422,429,502/503 |
| GET `/api/v1/assets/{id}/analyses/{run_id}` | consultar estado y cobertura | `{id,status,coverage,source_timestamp}` | 401,404 |
| GET `/api/v1/assets/{id}/analyses/{run_id}/findings` | consultar hallazgos | `{items:[{external_id,applicability,status,cvss}],page}` | 401,404 |
| PATCH `/api/v1/assets/{id}/analyses/{run_id}/findings/{finding_id}` | revisión humana futura | `{status:"reviewed"}`; ampliación de aplicabilidad requiere migración | 401,404,409,422 |

Ejemplos de nombres y estados son propuestas, no respuestas reales. Confirmación debe resolver UUID en catálogo confiable, capturar nombre y fecha; cambios no alteran análisis previos. POST análisis y confirmación requieren política idempotente propia por actor/ruta y huella, no reutilizar a ciegas tabla cuyo CHECK fija `/assets`. Nunca aceptar URL NVD ni credenciales de dispositivos desde cliente. Fijar host, HTTPS, límites, codificación y control de redirecciones, timeouts y cuota compartida; propagar error de origen sin exponer secretos. En caso de éxito parcial, cobertura `partial` y detalle no sensible; cero CVE no es fallo ni garantía de seguridad.

## JSON de referencia

Registro **existente**, compatible con AssetCreate inspeccionado (datos ficticios; no se hizo POST):
```json
{"device_type":"router","manufacturer":"Cisco","model":"RV340","version":"1.0.0.33","site_label":"LAB-A","inventory_label":null,"notes":null}
```
Selección **propuesta** de un candidato realmente devuelto por NVD:
```json
{"cpe_name_id":"C817E5BA-62C1-41E7-A15C-FB851FEA761E"}
```
Respuesta **simulada** del nuevo recurso; UUID y fecha local son ilustrativos:
```json
{"id":"11111111-1111-4111-8111-111111111111","cpe_name":"cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*","cpe_name_id":"C817E5BA-62C1-41E7-A15C-FB851FEA761E","confirmed_at":"2026-11-06T06:30:00Z"}
```
Solicitud de análisis **propuesta**:
```json
{"selection_id":"11111111-1111-4111-8111-111111111111"}
```
No reutilizar el UUID de un CPE como id de selección local. El endpoint de candidatos no expone datos de otro usuario: consulta catálogo público; las selecciones, análisis y hallazgos sí deben heredar autorización del activo. Verificar que run_id y finding_id pertenecen al activo autorizado, además de validar UUID; 404 ante recurso ajeno. Actualizar sesión de usuario activo conforme dependencia existente. Los GET existentes listan con `limit` 1–100 y `offset`; proponer igual patrón para hallazgos, con límites y campos públicos expresos.

## Seguridad de la integración propuesta

Fijar dominio services.nvd.nist.gov y rutas permitidas; construir query con codificación estándar; no seguir redirecciones a host arbitrario. No solicitar URL suministrada por cliente ni links de referencias CVE. Límite de query, longitud de CPE según parser y máximo de respuesta; clientes solo reciben datos saneados. NVD se consulta desde servicio backend, con presupuesto global compartido entre workers y caché acotada por parámetros, no por sitio ni usuario. API key futura solo en backend/gestor de secretos, nunca en cliente o logs. Evitar transacción SQL abierta durante espera de red; persistir estado de ejecución antes y resultados de manera consistente al final o por lotes marcados parciales. Una caída no produce hallazgo falso ni un segundo análisis por reintento de la misma clave.

Reintento limitado y retroceso con variación para error transitorio; detener o respetar indicación ante 403/429, no multiplicar workers para eludir cuota. Logs: request_id, estado, duración, páginas y causa no sensible; no guardar tokens ni notas de inventario en query. Validar fuente y estructura JSON, no ejecutar referencias ni HTML de descripciones. Los datos NVD son externos y pueden cambiar: conservar su lastModified, consulta local y huella del fragmento utilizado. ZAP queda excluido del MVP; futuro Baseline solo web autorizado, egress y DNS/redirecciones comprobados, nunca redes ni routers.
