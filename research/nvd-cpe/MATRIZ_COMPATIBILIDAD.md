# Matriz de compatibilidad

| Elemento | Estado actual verificado | Necesidad NVD / cambio propuesto | Compatibilidad y prioridad |
|---|---|---|---|
| assets.manufacturer | texto requerido 120 | término libre vs vendor normalizado | reutilizar dato; P0 |
| assets.model | texto requerido 120 | nombre comercial puede diferir del producto CPE | no convertir directamente; P0 |
| assets.version | texto requerido 120 | versión firmware/SO, sufijos | reutilizar si conocido; null requiere migración; P0 |
| assets.device_type | texto libre requerido 80 | orientar búsqueda, no es part CPE | reutilizar, lista controlada propuesta; P1 |
| analysis_runs.selected_cpe | requerido, CHECK prefijo | snapshot de selección y trazabilidad | reutilizar al ejecutar, falta selección previa/UUID; P0 |
| findings.external_id | CVE regex requerido | CVE más timestamps y deduplicación | reutilizar con información adicional; P0 |
| findings.applicability | solo `potential` | indeterminado/no aplicable con evidencia | requiere cambio CHECK/API; P1 |
| assets.created_by | en Backend 0002, no Database V1 | aislamiento por propietario | depende de coordinación y aprobación; P0 |
| asset_registration_requests | solo POST `/assets` | idempotencia de selección/análisis | no generalizar sin nuevo diseño; P1 |
| V2 technologies | CPE opcional por application/workspace | componentes físicos | conceptos reutilizables, entidades no integradas; P2 |
| Backend V1.3 | registro/lista/detalle, JWT | búsqueda, confirmación, ingestión | endpoints nuevos y servicios, no existen hoy; P0 |
| Contexto D-001/D-002 | aprobadas | coordinación SQL vs ORM/Alembic | respetar; P0 |

Sin migración puede conservarse el formulario y almacenarse un CPE como snapshot de una ejecución si existe mecanismo nuevo autorizado; no hay endpoint actual para crear esas ejecuciones. Para selección persistente, historial y versión desconocida se necesita evolución coordinada.
