# Modelo propuesto CPE y CVE

**VERIFICADO EN CÓDIGO:** V1 `assets` es inventario; `analysis_runs` guarda asset_id, initiated_by, selected_cpe, estado, cobertura, source_timestamp y fechas; `findings` guarda analysis_id, external_id, applicability `potential`, status `open/reviewed`, CVSS JSON y observed_at. No guarda por sí mismo el CPE confirmado antes del análisis, UUID de diccionario ni evidencia completa/versionado NVD. `asset_registration_requests` solo cubre la creación de activos.

**PROPUESTA A:** añadir selección principal versionada asociada a activo (id, asset_id, cpe_name, cpe_name_id, estado, confirmado_por/en, fuente y fin de vigencia). Crear nueva fila al cambiarla, nunca sobrescribir interpretación histórica. Una sola selección vigente mediante restricción/índice parcial; proteger accesos por propietario. `analysis_runs` captura selección y parámetros/fuente/cobertura; preservar selected_cpe como snapshot. `findings` reutiliza FK por ejecución y CVE; considerar unicidad `(analysis_id,source,external_id)` y guardar `lastModified`, evidencia limitada, revisión y resultado no determinado. Cualquier columna o nueva tabla requiere SQL coordinado, Alembic, privilegios, catálogo y migración. La aplicación actual solo admite `potential`, por tanto no prometer `not_applicable` sin evolución aprobada.

**PROPUESTA B:** `asset_components` (tipo, fabricante, producto, versión y procedencia), `component_cpe_selections` e historial 1:N; análisis referencia identificación congelada. Permite firmware y SO simultáneamente, pero aumenta tablas, autorización y UX; no tratar múltiples CPE como una conjunción automática.

| Criterio | A principal | B componentes |
|---|---|---|
| Integridad y autorización | menor superficie, una selección activa | FK e invariantes por componente, mayor esfuerzo |
| Rendimiento y mantenimiento | consultas simples | índices por activo/componente, historial más preciso |
| Cobertura | pierde otros componentes: mostrar limitación | múltiples productos representables, no prueba presencia |
| V1 / backend | extensión mínima, sin compatibilidad automática | nuevas relaciones y contratos |
| V2 | adaptable, no requiere workspaces | `technologies` similar pero ligado a applications/workspace; no reutilizable directamente para assets |

Recomendar A para cuatro semanas si se explicita que solo cubre un componente principal; el modelo B es evolución pendiente. No conservar toda NVD: snapshot mínimo por ejecución, configuración/rango pertinente, estado y fuente, timestamps y CVSS tal como se recibió; separar identificación CVE de conclusión sobre activo. No inferir inexistencia o explotación de CVE a partir de cero resultados.
