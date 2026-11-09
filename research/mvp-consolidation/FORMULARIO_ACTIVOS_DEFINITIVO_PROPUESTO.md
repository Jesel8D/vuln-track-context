# Formulario definitivo propuesto de activos

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

| Nombre funcional / técnico | Tipo y límite propuesto | Registro / identificación | Ejemplo y desconocido | CPE, PostgreSQL y V1.3 |
|---|---|---|---|---|
| Tipo / `device_type` | string estricto 1–80 | obligatorio registrar | `router`; desconocido exige elegir router/switch/server en UI, sin inventar categoría | inventario, no part; columna actual; conservar texto libre en API para históricos, UI MVP limitada |
| Fabricante / `manufacturer` | string 1–120 | obligatorio registrar, término de búsqueda | `Cisco`; desconocido impide registro conforme contrato actual | no equivale automáticamente a vendor; columna y API actuales |
| Modelo / `model` | string 1–120 | obligatorio registrar, orienta búsqueda | `RV340`; no sustituir desconocido por texto ficticio | modelo comercial puede diferir de product; columna/API actuales |
| Versión principal observada / `version` | string 1–120 o null | opcional registrar; requerida para solicitar evaluación específica en este MVP | `1.0.0.33` o null; conservar sufijos | NOT NULL y string hoy: requiere SQL/ORM/Pydantic y compatibilidad de clientes |
| Ubicación administrativa / `site_label` | string 1–120 | obligatorio registrar | `LAB-A`; nunca enviar a NVD | columna/API actuales, no es target_sw ni sitio web |
| Inventario / `inventory_label` | string 1–120 o null | opcional | `INV-01`; omitir/null, no vacío | columna/API actuales; no imponer unicidad nueva |
| Notas / `notes` | string 1–1000 o null | opcional | `Equipo de laboratorio`; sin secretos | columna/API actuales; no analizar como query NVD |
| Nombre administrativo / `display_name` | no persistir en MVP | opcional, derivado de manufacturer + model | presentación `Cisco RV340` | no añadir columna ni aceptar propiedad JSON nueva |
| Producto o SO observado / `product_query` | string 1–160 | opcional en búsqueda; reemplaza consulta ambigua, no fabricante del inventario | `Small Business RV340 Firmware`; vacío se omite | no es campo de AssetCreate; solo query efímera; candidato confirma producto real |
| Componente principal / `component_kind` | enum `hardware`, `firmware`, `os`, `software` | obligatorio al confirmar | `firmware`; sin elección no confirmar | nuevo campo de identificación; no deducir part exclusivamente de categoría de activo |
| Versión observada de la identificación / `observed_version` | string 1–120 o null, copiada de assets.version al crear evento | backend; solo lectura | `1.0.0.33` o null | snapshot; versión del CPE se conserva separada, no altera inventario |
| Nombre CPE / `cpe_name` | string parseable CPE 2.3, máximo 1024 | derivado NVD; nunca obligatorio registrar | nombre real retornado por catálogo | nuevo en identificación, snapshot ya existe en analysis_runs.selected_cpe |
| ID de diccionario / `cpe_name_id` | UUID | obligatorio confirmar estado confirmed, derivado NVD | `C817E5BA-62C1-41E7-A15C-FB851FEA761E` | no confundir con matchCriteriaId; columna nueva |
| Estado / `state` | `pending`, `confirmed`, `needs_review` | backend según operación explícita | sin fila: pending; cero candidatos es resultado efímero | nuevo recurso, no renombrar status del análisis |
| Actor y fecha / `recorded_by`, `recorded_at` | UUID autenticado, timestamptz UTC | generados backend | nunca aceptados del cliente | FK users; nuevo historial; confirmación es recorded_by/at cuando state=confirmed |
| Fuente y validación / `source`, `validated_at` | `NVD`, timestamptz UTC | backend al confirmar | sin conexión válida no confirmar selección nueva | nuevo snapshot de procedencia |
| Propietario / `created_by` | UUID, nullable solo para legado | generado backend | actor de JWT, nunca body | presente en ORM y 0002; ausente en baseline Database V1 |
## Reglas normativas propuestas

Conservar cinco campos de inventario requeridos salvo `version`, que pasaría a nullable. String estricto, NFC y strip; rechazar vacío y caracteres de control/surrogates, extras desconocidos prohibidos. Para opcionales, omitido o null equivale a desconocido; no convertir `"unknown"`, `"*"` o `"-"` en versión real. Las longitudes son caracteres normalizados y se verifica además el límite de cuerpo HTTP actual de 8192 bytes. IP, GPS, puertos, credenciales y servicios expuestos no son campos de registro.

La versión se refiere al componente principal que el usuario desea identificar. En confirmación mostrar explícitamente componente y versión observada; si la versión de inventario no describe el producto elegido, rechazar la confirmación como identificación suficientemente específica y explicar que se necesita corrección de inventario. La edición general de activos sigue POSTERIOR; para una demo actual se puede registrar otro activo correcto, sin borrar ni reasignar el anterior. No prometer completar la versión después sin aprobar e implementar edición. Puede confirmarse una selección incompleta con version null, pero no solicitar evaluación CVE específica: 409 VERSION_REQUIRED.

`product_query` es ayuda de búsqueda, no dato persistente nuevo del activo; no añadir dos versiones de SO/firmware al formulario del MVP. Un servidor físico puede tener SO como componente principal: fabricante/modelo del inventario siguen siendo observados del equipo y la búsqueda tecnológica puede usar fabricante/producto del SO; la pantalla debe evitar confundir ambos. Para producto principal distinto del hardware, la consulta `query` es editable y no se compone siempre a partir del fabricante físico.

## Boceto e interacción

```text
Activo: [router | switch | servidor]
Fabricante [Cisco]  Modelo [RV340]
Versión del componente principal [1.0.0.33] [ ] No la conozco
Ubicación [LAB-A]  Etiqueta opcional [ ]
Notas no sensibles [ ]                       [Guardar activo]
Identificación tecnológica (después de guardar)
Buscar producto [Cisco RV340 firmware]        [Buscar candidatos]
Componente [firmware] Versión observada [solo lectura]
Candidatos: fabricante/producto/versión/update/plataforma/deprecado
[Confirmar candidato]  [Mantener pendiente]
```

Ejemplo JSON PROPUESTO tras adaptación: `{"device_type":"router","manufacturer":"Cisco","model":"RV340","version":null,"site_label":"LAB-A","inventory_label":null,"notes":null}`. Ese null **hoy devuelve validación fallida**. Errores orientados al usuario: «Indica fabricante/modelo/sitio»; «Versión desconocida: puedes registrar, pero no evaluar de forma específica»; «La versión o componente no concuerda: revisa tu elección»; «No hay candidatos para esta búsqueda; el activo sigue guardado». La selección nunca es automática y la pérdida de red conserva formulario y clave del mismo intento.
