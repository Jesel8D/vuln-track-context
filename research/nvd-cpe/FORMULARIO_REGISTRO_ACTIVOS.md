# Formulario propuesto de activos

**VERIFICADO EN CÓDIGO:** el contrato actual exige `device_type`, `manufacturer`, `model`, `version`, `site_label`; `inventory_label` y `notes` opcionales. Strings estrictos normalizados NFC/sin extremos, sin caracteres Cc/Cs; límites 80, 120 y 1000. No acepta campos adicionales. **PROPUESTA:** permitir versión desconocida exige cambio SQL y API, no introducir cadena falsa para superar el contrato actual.

| Campo / tipo / ejemplo | Registro | Búsqueda / evaluación | Validación y mensaje |
|---|---|---|---|
| tipo texto, router | obligatorio | orienta búsqueda | 1–80, categoría explícita; «Selecciona un tipo» |
| fabricante texto, Cisco | obligatorio | requerido para búsqueda orientada | 1–120; «Indica fabricante» |
| modelo texto, RV340 | obligatorio | recomendado para desambiguar | 1–120; «Indica modelo» |
| versión observada texto | opcional propuesto; obligatorio hoy | una versión concreta es necesaria para evaluación específica; conservar sufijos | 1–120 o null tras migración; «Versión desconocida: evaluación limitada» |
| sitio texto | obligatorio hoy | no se envía a NVD | 1–120; «Indica ubicación» |
| etiqueta inventario texto | opcional | no | null o 1–120; «Etiqueta demasiado larga» |
| notas texto | opcional | no | null o 1–1000, sin secretos; «No incluyas credenciales» |
| nombre interno | opcional futuro | no | proponer solo con columna/contrato; no es campo actual |
| producto/componente y SO/firmware | opcional avanzado propuesto | mejora búsqueda | texto observado y versión; no confundir con modelo de hardware |
| CPE Name y UUID | generado por elección de candidato NVD | confirmar para consulta específica | UUID y nombre verificados en diccionario; «Selecciona un candidato» |
| estado y fecha | backend | trazabilidad | pendientes/confirmado/sin coincidencia; tiempo UTC de servidor |

**Interacción:** guardar inventario → «Buscar identificación» → ver fabricante, producto, versión, descripción, deprecated y enlace NVD → usuario confirma o deja pendiente. Sin resultados ofrecer editar términos/guardar pendiente; varios candidatos obligan a revisar, no elegir primero. Versión ausente permite registrar y buscar de modo amplio, no declarar análisis específico. Al cambiar identificación, conservar selección anterior, actor/fecha y resultados históricos; marcar evaluaciones anteriores como no actuales. Boceto: `Tipo [router] Fabricante [ ] Modelo [ ] Versión [desconocida] Sitio [ ]` / `Avanzado: producto/firmware [ ]` / `[Guardar] [Buscar CPE]` / `Candidatos […] [Confirmar]`.

## Campos actuales, inventario e identificación

| Campo actual | Origen | Utilidad | Relación con CPE | Limitación | Recomendación |
|---|---|---|---|---|---|
| id UUID | backend/SQL sin default UUID | identidad del inventario | ninguna | no identifica producto | conservar |
| device_type | AssetCreate y assets | categoría libre | no equivale a part | router y firmware son distintos | orientar UX |
| manufacturer | AssetCreate y assets | fabricante observado | candidato vendor | alias y renombres | conservar original, mapear por candidato |
| model | AssetCreate y assets | modelo comercial | puede orientar product | familia no basta | pedir variante/revisión si existe |
| version | AssetCreate y assets | versión observada | orientar version/update | no declara componente | pedir etiqueta firmware/SO en selección avanzada |
| site_label | AssetCreate y assets | ubicación administrativa | ninguna | dato innecesario para NVD | no enviarlo |
| inventory_label | opcional | etiqueta administrativa | ninguna | no es única en SQL | conservar |
| notes | opcional | observaciones no sensibles | no usar automáticamente | puede contener secretos | aviso y minimización |
| created_at/updated_at | SQL/backend | tiempo local | no son fechas NVD | no indican recencia del catálogo | conservar separadas |
| analysis_runs.selected_cpe | SQL de ejecución | selección congelada | nombre CPE | sin UUID ni confirmación persistente | ampliar trazabilidad |
| findings.external_id | SQL de hallazgo | identificador CVE | relación indirecta por análisis | regex no prueba existencia | validar respuesta NVD |

Para búsqueda inicial: fabricante y producto/modelo son suficientes como términos, versión mejora precisión pero no debe inventarse. Para evaluación específica: identificación principal confirmada, versión observada conocida cuando el producto lo requiere y revisión de condiciones de plataforma. El fabricante no necesita conocer sintaxis CPE; no pedir UUID ni todos sus atributos como campos de texto. En «Avanzado» mostrar nombre/versión de firmware o SO, revisión hardware, fuente observada y candidato completo en modo lectura. Esta información adicional requiere acordar persistencia; no enviarla al POST actual como si fuese aceptada.
