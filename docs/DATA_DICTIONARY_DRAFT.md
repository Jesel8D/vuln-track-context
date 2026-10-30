# Diccionario de datos conceptual — borrador

Todo atributo, tipo, restricción, relación, índice y ciclo descrito es **PROPUESTA**, con aprobación **PENDIENTE**. No es esquema SQL ni compromete implementación. Cuatro entidades conceptuales originales permanecen sin relaciones en [data_model.mmd](../diagrams/data_model.mmd). Tipos `text`, `timestamp`, `json` y `id` son conceptuales; almacenamiento y límites concretos no están aprobados. `R` = requerido en esta propuesta; `O` = opcional.

## User

**Propósito:** identidad de usuario de VulnTrack; no es cuenta administrativa de dispositivos. Ciclo propuesto: alta controlada, desactivación y retención por definir; jamás almacenar contraseña en texto. Dependencias: esquema de identidad, roles y políticas de acceso **PENDIENTE**.

| Atributo | Tipo | Oblig. | Validación / sensibilidad |
|---|---|---|---|
| id | id | R | Único, inmutable; no extrapolar tipo final. |
| username | text | R | No vacío, longitud y normalización por acordar; dato personal posible. |
| password_hash | text | O | Solo si se aprueba autenticación local; hash seguro con algoritmo y parámetros por aprobar; sensible, nunca en API/logs. |
| active | boolean | R | Solo identidad activa accede; por acordar. |
| created_at | timestamp | R | UTC, servidor; dato de auditoría. |

**Índices/restricciones:** unicidad de username normalizado solo después de definir colación; índice de clave primaria. Permisos/roles no se modelan como entidad nueva en este borrador; representación pendiente.

## Asset

**Propósito:** inventario capturado en campo; no es instrucción de escaneo. **Ciclo:** creación y consulta establecidas; cambios/eliminación y retención **PENDIENTE**. Dependencias: validación, idempotencia y permisos.

| Atributo | Tipo | Oblig. | Validación / sensibilidad |
|---|---|---|---|
| id | id | R | Identificador único asignado por servidor. |
| device_type | text | R | Tipo de dispositivo, incluido dispositivo de red; catálogo pendiente. |
| manufacturer | text | R | No vacío; longitud pendiente. |
| model | text | R | No vacío; no garantiza CPE. |
| version | text | R | Versión firmware o software; no interpretar semántica automáticamente. |
| site_label | text | R | Identificador o etiqueta del sitio; puede revelar ubicación organizativa. |
| inventory_label | text | O | Etiqueta opcional; unicidad por sitio y norma pendientes. |
| notes | text | O | Solo observaciones no sensibles; no garantiza que filtro automático detecte secretos. |
| created_at | timestamp | R | Momento de registro propuesto. |

**Índices:** id; considerar `site_label`, y `inventory_label` solo tras acordar búsquedas y alcance. No agregar IP, contraseña, GPS ni datos personales sin decisión. Selección de CPE puede almacenarse como atributo del análisis y no como identidad exacta del activo.

## AnalysisRun

**Propósito:** historial de consulta NVD o ejecución ZAP; unión conceptual propuesta, no implica que una consulta y un scan compartan todos los datos. **Ciclo:** crear, ejecutar y finalizar; duración, cancelación, reintento, actualización y retención pendientes. Dependencias: destino preautorizado, CPE elegido y alcance por recurso.

| Atributo | Tipo | Oblig. | Validación / sensibilidad |
|---|---|---|---|
| id | id | R | Único. |
| kind | text | R | Propuesta `nvd_cve` o `zap_baseline`; lista por aprobar. |
| asset_id | id | O | Para NVD, referencia al activo consultado; opcional para ZAP, si se aprueba relación. |
| target_id | id | O | Solo ZAP; referencia a entrada autorizada gestionada por servidor; almacenamiento de lista aún pendiente, no define nueva entidad. |
| selected_cpe | text | O | Para NVD, selección manual, formato y versión válidos; no inferir exactitud. |
| status | text | R | Propuesta `queued`, `running`, `completed`, `failed`; transiciones por aprobar. |
| coverage | text | O | `complete`/`partial` para recuperación NVD, no certeza de aplicabilidad. |
| started_at / completed_at | timestamp | O | Orden temporal; pueden faltar antes de ejecución/finalización. |
| source_timestamp | timestamp | O | Momento de fuente/consulta, procedencia explícita. |

**Restricciones:** `kind` determina campos requeridos; no guardar URL arbitraria, claves, parámetros de ejecución libres o reportes sin política. Índices potenciales `(asset_id, started_at)`, `(kind, status)` y `target_id` si se justifica. La lista permitida y su identificación son dependencias no modeladas definitivamente.

## Finding

**Propósito:** evidencia de CVE potencial de NVD o alerta de ZAP; no son semánticamente idénticos. **Ciclo:** captura, revisión humana y conservación histórica; confirmación, falso positivo, transición y cierre pendientes. Dependencia: análisis y autorización por recurso.

| Atributo | Tipo | Oblig. | Validación / sensibilidad |
|---|---|---|---|
| id | id | R | Único. |
| analysis_id | id | R | Referencia propuesta al análisis originador. |
| source | text | R | `NVD` o `ZAP` propuestos; debe coincidir con kind. |
| external_id | text | O | CVE si NVD o identificador de regla ZAP; no deducir CVE de alerta. |
| title | text | O | Título saneado; puede contener datos externos. |
| applicability | text | O | Para CVE `potential` hasta verificación; no aplica automáticamente a alertas ZAP. |
| status | text | R | `open`/`reviewed` propuestos; revisión no implica confirmar. |
| cvss | json | O | Versión, vector, valor, fuente y fecha si están disponibles; no inventar cero. |
| observed_at | timestamp | R | Instante y procedencia propuestos; evitar evidencia sensible. |

**Índices:** `(analysis_id, source)`, `external_id` según filtros y volumen; unicidad y deduplicación **PENDIENTE**, ya que el mismo CVE puede aparecer en ejecuciones distintas. ZAP puede dar múltiples instancias de una regla; decidir granularidad antes de imponer unicidad.

## Relaciones y conservación

**PROPUESTA:** un Asset puede tener cero o muchos AnalysisRun NVD; cada AnalysisRun NVD corresponde a un Asset; un AnalysisRun puede producir cero o muchos Finding y cada Finding pertenece a uno; un User puede iniciar muchas ejecuciones y cada ejecución puede registrar un iniciador. El vínculo ZAP-destino y acceso de usuarios no están definidos: podría requerirse estructura adicional previa aprobación. `User` no debe implicar que cualquier usuario puede ver cualquier activo.

**PENDIENTE:** claves foráneas y nulabilidad, reglas al borrar, snapshots de CPE/destino, evidencia de cambios, concurrencia, retención y auditoría. **PROPUESTA:** integridad referencial restrictiva mientras se acuerda historial, almacenar fecha y procedencia del resultado y no sobrescribir resultados antiguos sin política. Normalizar identidad y referencias, evitando duplicar datos mutables; si se guarda snapshot, especificar que es histórico, no verdad actual. No se decide esquema ni cardinalidad final.
