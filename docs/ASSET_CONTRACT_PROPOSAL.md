# Contrato mínimo propuesto para activos

Todo esquema, regla, límite, código y ejemplo es **PROPUESTA**, sujeto a aprobación; no es código implementado. Complementa [API inicial](API_CONTRACT.md), [borrador API](API_CONTRACT_DRAFT.md) y [requisitos](REQUIREMENTS.md). **ESTABLECIDA:** móvil registra activos, conserva datos al fallar y permite reintentar; consulta en web, enfoque online-first; no se aprueban actualización ni eliminación. API propuesta en JSON UTF-8, fechas RFC 3339 en UTC, HTTPS y autorización por recurso. No requiere GPS, cámara ni secretos de clientes.

## Campos de entrada

La obligatoriedad del borrador es **PROPUESTA**: los datos contemplados están establecidos, pero las reglas definitivas están **PENDIENTE**. Todas las longitudes cuentan caracteres Unicode tras recorte, no bytes; aplicar límite separado de tamaño de cuerpo, p. ej. 8 KiB, propuesto. Preservar caracteres y mayúsculas; no convertir versiones a número ni imponer formato semver. Recortar espacios al inicio y fin; validar cadena no vacía tras recorte; no cambiar el contenido interior ni almacenar HTML sin codificación al mostrarlo.

| Campo | Tipo | Presencia propuesta | Longitud / validación propuesta | Origen / límite |
|---|---|---|---|---|
| `device_type` | cadena | requerida | 1–80; texto libre, catálogo **PENDIENTE** | Tipo de dispositivo establecido, incluidos equipos de red; su registro no autoriza escaneo. |
| `manufacturer` | cadena | requerida | 1–120; no vacía | Fabricante establecido. |
| `model` | cadena | requerida | 1–120; no vacía | Modelo establecido. |
| `version` | cadena | requerida | 1–120; firmware o software como texto | Versión establecida; formato y desconocido **PENDIENTE**. |
| `site_label` | cadena | requerida | 1–120; no vacía | Identificador o etiqueta del sitio; no GPS ni dirección exigida. |
| `inventory_label` | cadena o null | opcional | 1–120 cuando se envía; ausente o null equivale a null; cadena vacía rechazada | Etiqueta opcional; unicidad **PENDIENTE**. |
| `notes` | cadena o null | opcional | 1–1000 cuando se envía; ausente o null equivale a null; cadena vacía rechazada | Observaciones no sensibles; la validación automática no garantiza detectar secretos. |

No aceptar campos desconocidos; no incluir contraseñas administrativas, secretos de clientes, IDs de propietario ni URL de análisis. Las longitudes exactas, carácter obligatorio, normalización de Unicode, catálogo y manejo de valores desconocidos requieren decisión humana. No deducir CPE de estos campos.

## Creación — POST /assets

**PROPUESTA:** requiere sesión y `asset:create`; `Content-Type: application/json`, `Accept: application/json` e `Idempotency-Key` por intento lógico. La autenticación definitiva y permisos están **PENDIENTE**. El cliente conserva formulario y clave si no conoce el resultado; no presenta éxito sin confirmación. Ejemplo sintético:

```json
{"device_type":"router","manufacturer":"Marca ejemplo","model":"Modelo A","version":"1.2","site_label":"SITIO-1","inventory_label":null,"notes":null}
```

**Response 201 PROPUESTA:** encabezado `Location: /assets/8b1b17ef-097f-49fd-ac45-221bb697867c` y cuerpo:

```json
{"id":"8b1b17ef-097f-49fd-ac45-221bb697867c","device_type":"router","manufacturer":"Marca ejemplo","model":"Modelo A","version":"1.2","site_label":"SITIO-1","inventory_label":null,"notes":null,"created_at":"2026-10-30T12:00:00Z"}
```

`id` UUID generado por servidor y `created_at` UTC desde servidor son **PROPUESTA** técnica, no nuevos datos de negocio obligatorios. No permitir que el cliente los asigne. Alternativa: ID opaco de otro formato; se requiere decidir. Reintento idéntico dentro de la ventana devuelve el mismo 201, `Location` y cuerpo; si se cambió el contenido, conflicto. Las etiquetas pueden repetirse hasta que se apruebe unicidad.

## Consulta — GET /assets y GET /assets/{id}

**PROPUESTA:** `asset:read` y filtro por recursos autorizados. Listado acepta `limit` entero 1–100, defecto 20, y `offset` entero ≥0, defecto 0; ordenar por `(created_at, id)` para desempate; resultados pueden cambiar entre páginas si se agregan registros. `total` cuenta solo registros visibles. Ejemplo:

```json
{"items":[{"id":"8b1b17ef-097f-49fd-ac45-221bb697867c","device_type":"router","manufacturer":"Marca ejemplo","model":"Modelo A","version":"1.2","site_label":"SITIO-1","inventory_label":null,"notes":null,"created_at":"2026-10-30T12:00:00Z"}],"page":{"limit":20,"offset":0,"total":1}}
```

Detalle devuelve el mismo objeto con 200; no encontrado o no visible 404 propuesto. Paginación basada en cursor es alternativa para cambios concurrentes, con mayor complejidad; elegir antes de integrar clientes.

## Errores y seguridad

**PROPUESTA:** `400` JSON malformado/parámetro de consulta inválido, `401` sin identidad válida, `403` acción prohibida, `404` recurso no visible o ausente, `409` clave repetida con cuerpo distinto o intento en curso, `422` datos semánticamente inválidos, `429` límite, `500` fallo interno y `503` base temporalmente indisponible. Respuesta uniforme, sin contenido sensible:

```json
{"error":{"code":"VALIDATION_ERROR","message":"Solicitud inválida","request_id":"req-ejemplo","fields":[{"field":"manufacturer","message":"Requerido"}]}}
```

`request_id` es correlación técnica propuesta; `fields` opcional. Los códigos exactos, estrategia 403/404, rate limiting y semántica de fallos de persistencia están **PENDIENTE**. No registrar el cuerpo completo, claves de sesión ni observaciones. Clientes deben diferenciar fallo definitivo de resultado incierto.

## Idempotencia — PROPUESTA

1. Cliente genera una clave opaca aleatoria por envío lógico y la reutiliza solo para el mismo cuerpo y usuario; envía una nueva ante modificación o nueva operación. Propuesta: UUID generado por cliente, longitud validada.
2. Servidor limita el ámbito por usuario, método y ruta, calcula huella del cuerpo validado y normalizado y reserva clave con restricción única en base de datos. La reserva, alta y resultado recuperable se confirman en la misma transacción; una excepción revierte todo. Dos peticiones simultáneas con misma clave se serializan; si no se puede esperar, `409` temporal propuesto. Antes de devolver un resultado repetido se vuelven a comprobar identidad y autorización.
3. Misma clave y huella devuelve la respuesta almacenada, mismo código, `Location` y cuerpo. Misma clave con huella distinta: `409` sin nuevo activo. Mantener la respuesta y huella mínimas protegidas por la misma política de acceso de activos.
4. Ventana ilustrativa **PROPUESTA** de 24 horas tras confirmación; debe negociarse según conectividad. Para un resultado incierto, el cliente calcula un límite conservador desde el primer intento, no desde la respuesta desconocida; si el reloj, la fecha guardada o la duración aprobada no son fiables, no reintenta automáticamente. Cuando llegue ese límite, el cliente no debe reintentar silenciosamente el envío incierto con la misma clave: se requiere reconciliación manual o comprobación autorizada; política de expiración, recuperación y conservación local de la marca temporal son **PENDIENTE**. No se garantiza deduplicación por dos claves diferentes ni después de expirar.

Probar desconexión antes de commit, después de commit y antes de respuesta, concurrencia, cambios de cuerpo, revocación de acceso y caducidad. El requisito de retener formulario tras fallo no establece que sobreviva el cierre de la app; protección y duración del almacenamiento local están **PENDIENTE**. La idempotencia no constituye sincronización offline completa.
