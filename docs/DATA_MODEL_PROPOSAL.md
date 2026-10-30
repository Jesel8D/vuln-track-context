# Modelo de datos propuesto

**PROPUESTA:** los nombres, tipos, claves, relaciones, restricciones e índices siguientes requieren aprobación; no son migraciones ni ORM. Los datos identificados en requisitos se separan de atributos inferidos de operación. El diagrama original [data_model.mmd](../diagrams/data_model.mmd) permanece con nodos independientes. [Diccionario anterior](DATA_DICTIONARY_DRAFT.md) contiene contexto adicional.

## Asset

| Atributo | Tipo propuesto / nulabilidad | Origen y controles propuestos |
|---|---|---|
| `id` | UUID, PK, no null | Inferido: generado por servidor. |
| `device_type`, `manufacturer`, `model`, `version`, `site_label` | texto, no null | Datos contemplados; obligatoriedad y límites de [contrato](ASSET_CONTRACT_PROPOSAL.md) son PROPUESTA. |
| `inventory_label`, `notes` | texto, null | Datos opcionales contemplados; notas no sensibles; no imponer unicidad sin decisión. |
| `created_at` | timestamp con zona, no null | Inferido para auditoría; UTC a nivel de API. |

Índices propuestos: PK e índice `(created_at,id)` para orden; por sitio solo si se aprueban filtros. `updated_at`, `created_by` y borrado lógico requieren aprobación, igual que actualización/eliminación.

## User

| Atributo | Tipo propuesto / nulabilidad | Origen y controles propuestos |
|---|---|---|
| `id` | UUID PK, no null | Inferido, identidad técnica. |
| `username` | texto, no null | Inferido para identidad local; normalización y unicidad por acordar. |
| `password_hash` | texto nullable | Solo si identidad local aprobada; hash adaptativo con parámetros aprobados; no contraseñas de equipos. |
| `active`, `created_at` | boolean y timestamp con zona, no null | Inferidos para revocación y auditoría. |

Permisos/roles pueden requerir estructuras adicionales; su esquema y responsables están **PENDIENTE**. Índice único de username solo tras definir normalización y semántica de identidad.

## AnalysisRun

| Atributo | Tipo propuesto / nulabilidad | Origen y controles propuestos |
|---|---|---|
| `id` | UUID PK, no null | Inferido. |
| `kind` | texto, no null | Inferido: `nvd_cve` o `zap_baseline`; catálogo **PENDIENTE**. |
| `asset_id` | UUID FK Asset, nullable | Propuesta: requerido para NVD; ZAP puede no referirse a un activo físico. |
| `initiated_by` | UUID FK User, no null | Inferido para auditoría; identidad y tratamiento de usuarios eliminados **PENDIENTE**. |
| `selected_cpe` | texto nullable | Para NVD, selección explícita; no prueba coincidencia exacta. |
| `target_id` | UUID nullable | Para ZAP, identificador de lista autorizada; entidad de destinos, FK y almacenamiento **PENDIENTE**. |
| `status`, `coverage` | texto; status no null, coverage nullable | Estado de ejecución y completitud de consulta propuestos; no confundir con estados formales de proyecto. |
| `started_at`, `completed_at` | timestamp con zona nullable | Inferidos, orden y retención por acordar. |

Restricción propuesta por `kind`: NVD necesita `asset_id` y `selected_cpe`; ZAP necesita `target_id` y no admite URL libre del cliente. La persistencia de destino y su integridad requieren decisión separada. Índices propuestos `(asset_id, started_at)` y `(kind,status)` según consultas.

## Finding

| Atributo | Tipo propuesto / nulabilidad | Origen y controles propuestos |
|---|---|---|
| `id` | UUID PK, no null | Inferido. |
| `analysis_id` | UUID FK AnalysisRun, no null | Relación propuesta, sin cascada automática. |
| `source`, `external_id` | texto no null / nullable | NVD o ZAP; CVE y regla ZAP no son equivalentes. |
| `applicability`, `status` | texto nullable / no null | `potential` para CVE hasta verificación; revisión/estado por aprobar. |
| `cvss` | JSON nullable | Fuente, versión, vector y puntuación si existen; nunca asumir cero. |
| `observed_at` | timestamp con zona, no null | Inferido para trazabilidad. |

Índices propuestos `(analysis_id,id)` y `external_id` según acceso; no imponer unicidad de CVE global porque puede aparecer en distintos activos y ejecuciones.

## Cardinalidades, historial e idempotencia

**PROPUESTA:** Asset 1 → 0..N AnalysisRun NVD; User 1 → 0..N AnalysisRun iniciadas; AnalysisRun 1 → 0..N Finding. Los ZAP pueden requerir destino autorizado independiente; relación exacta no aprobada. FKs restrictivas evitan borrar historial accidentalmente, pero política de eliminación, anonimización, snapshots y retención está **PENDIENTE**. La normalización evita duplicar atributos actuales; un snapshot de CPE o resultado debe identificarse como histórico.

**PROPUESTA técnica adicional:** registro de idempotencia separado con PK o unicidad `(user_id, method, route, key)`, huella, respuesta, `created_at` y `expires_at`, asociado al resultado de creación; su estructura y FK específicas no son entidad de negocio aprobada. Debe participar de la misma transacción de creación y cumplir [contrato](ASSET_CONTRACT_PROPOSAL.md). Otras entidades necesarias para roles y destinos son **PENDIENTE**, no se presupone que las cuatro entidades basten.

**APROBADA D-002:** modelos SQLAlchemy y migraciones Alembic pertenecen a backend; database gestiona configuración PostgreSQL, inicialización, seed y documentación. Tecnologías y esquema siguen **PROPUESTA**. No se generó SQL.
