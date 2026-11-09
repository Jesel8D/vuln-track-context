# Fundamentos CPE, NVD y CVE

**VERIFICADO EN NVD:** un CPE Name describe una clase de producto, no un equipo físico individual. El UUID `cpeNameId` identifica una entrada del diccionario; `matchCriteriaId` identifica un criterio de emparejamiento y no son intercambiables. Un criterio puede ser una expresión abstracta con rango; CVE usa configuraciones con AND/OR y nodos que pueden indicar componentes necesarios, no solo un CPE aislado. Revisar `vulnerable`, negaciones, y límites inclusivos/exclusivos de versión. Si no se tienen todos los datos o la evaluación lógica no está implementada, mostrar **no determinado / requiere revisión**. [NVD: criterios](https://nvd.nist.gov/general/faq-sections/cpe-faqs), [configuraciones](https://nvd.nist.gov/vuln/Vulnerability-Detail-Pages).

Un nombre formateado es `cpe:2.3:part:vendor:product:version:update:edition:language:sw_edition:target_sw:target_hw:other` con escapes formales. `*` es ANY y `-` es NA; no inventar NA ni convertir una versión desconocida en una versión concreta. Una búsqueda y una cadena sintácticamente correcta no validan que describa el activo. Fuente: [NIST IR 7695](https://csrc.nist.gov/pubs/ir/7695/final).

| Componente | Significado / origen práctico |
|---|---|
| part | a aplicación, o sistema operativo, h hardware; candidato NVD sujeto a revisión |
| vendor | fabricante normalizado por NVD; comparar con denominación del usuario |
| product | producto normalizado; no siempre modelo comercial |
| version | versión en catálogo; comparar con versión observada, no asumir equivalencia |
| update | actualización o revisión |
| edition | edición heredada |
| language | idioma |
| sw_edition | edición de software |
| target_sw | plataforma software destino |
| target_hw | arquitectura o plataforma hardware destino |
| other | atributos adicionales |

Usuario aporta fabricante, modelo, versión y evidencia; NVD provee denominaciones, UUID, estado deprecated y metadatos. El backend normaliza entrada NFC y espacios sin convertir ciegamente a CPE; utiliza los campos del catálogo seleccionado y no pide manualmente los 11 atributos. Opcionales no son automáticamente desconocidos: conservar valor original y cualquier incertidumbre.

**API:** [Products 2.0](https://nvd.nist.gov/developers/products) usa `keywordSearch` (términos AND y coincidencia por prefijo), `cpeMatchString`, `cpeNameId`, `startIndex`, `resultsPerPage`; `products`, `totalResults`, `timestamp` y `deprecated` son evidencia. Una página con 0 resultados es distinta de error; resultados parciales no demuestran ausencia. [Vulnerabilities 2.0](https://nvd.nist.gov/developers/vulnerabilities) es la referencia para `cpeName` y `virtualMatchString`; `cpeName` fue probado, mientras `virtualMatchString` y sus parámetros de rango quedan **NO VERIFICADOS** en esta investigación por falta de contenido legible de la página y no se recomiendan para la primera implementación; distinguir búsqueda de diccionario de búsqueda CVE. Paginación por offset hasta `totalResults`, con límites de tiempo y política explícita de cobertura parcial. `lastModified` de CVE y fecha de consulta son cosas distintas; conservar ambas. [Inicio y uso de API](https://nvd.nist.gov/developers/start-here). Sin clave, presupuestar conservadoramente solicitudes e incorporar límites y retroceso centralizados; no usar reintentos para evadir 403/429. Registrar timeout, error, estado y páginas recuperadas; cero coincidencias no significa activo seguro.

## Política mínima de identificación y correlación — PROPUESTA

Tres estados persistentes bastan: `pending`, `confirmed`, `needs_review`; «sin coincidencia» puede ser resultado fechado de búsqueda y no una afirmación permanente. Mostrar «candidatos encontrados» como estado de interfaz, sin tabla de estados adicional. Un UUID de candidato se vuelve a resolver antes de confirmar; rechazar entradas deprecadas o exigir revisar su reemplazo y nunca cambiarlo silenciosamente. Conservar la selección previa y sus análisis. No limpiar sufijos de versión ni convertir semver, fechas, hotfix y nombres de rama a la misma comparación. No comparar lexicográficamente `1.10` con `1.9`.

| Situación | Comportamiento |
|---|---|
| Cero candidatos | guardar activo, registrar términos/fecha y permitir búsqueda manual posterior |
| Varios candidatos | mostrar part, producto, versión, update y plataforma; confirmar explícitamente |
| Vendor o producto renombrado | conservar nombre observado y nombre NVD, fuente y reemplazo; sin sustitución automática |
| Firmware distinto del hardware | indicar qué componente se identifica; la versión hardware no es la versión firmware |
| Versión desconocida | búsqueda amplia; consulta específica y conclusión de aplicabilidad bloqueadas o señaladas como indeterminadas |
| CPE equivocado | nueva selección con motivo, conservar historial; invalidar vigencia de resultados previos para el estado actual |
| Varios componentes | un principal para MVP con aviso de cobertura, múltiples relaciones solo tras aprobar alternativa B |

Evaluador conservador: recuperar CVE para identificación confirmada → guardar configuración pertinente → comprobar producto/versión y entorno cuando sea posible → devolver potencial con condiciones pendientes o no determinado. Un nodo `vulnerable=false` puede describir plataforma necesaria y no significa automáticamente CVE no aplicable. Un AND requiere todas sus condiciones; un OR puede satisfacerse con una; no ignorar negación. Conocimiento insuficiente no equivale a falso. Reservar «no aplicable» para una revisión documentada que contraste evidencia del activo con los criterios; no incluir confirmación de explotación.

Respuesta externa incompleta: validar JSON y esquema mínimo, tamaño, campos obligatorios y consistencia de página; conservar elementos válidos solo con cobertura parcial, registrar causa y no publicar `complete` si faltan páginas. Datos de una ejecución pueden cambiar entre páginas: capturar timestamps, deduplicar por CVE y no afirmar snapshot transaccional de NVD. Actualización posterior crea una ejecución, no borra resultados previos; almacenar fecha de fuente cuando exista y fecha local por separado.
