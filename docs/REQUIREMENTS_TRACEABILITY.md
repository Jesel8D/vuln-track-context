# Matriz de trazabilidad

La columna estado clasifica el alcance, no el criterio propuesto. Los criterios verificables y sus valores concretos son **PROPUESTA** hasta aprobación. Origen: `R` = [requisitos](REQUIREMENTS.md), `S` = [seguridad](SECURITY_BOUNDARIES.md), `A` = [API inicial](API_CONTRACT.md), `M` = misión del 30-10-2026; `C` = [contexto](PROJECT_CONTEXT.md). Dependencias se refieren a IDs de esta matriz. Los casos AC se detallan en [criterios](ACCEPTANCE_CRITERIA.md).

| ID | Módulo y nombre | Descripción | Origen | Estado | Dependencias | Criterio de aceptación propuesto | Pregunta abierta |
|---|---|---|---|---|---|---|---|
| RF-01 | Usuarios: identidad | Inicio de sesión e identidad del usuario | A | PROPUESTA | RN-01 | Credencial válida produce sesión, inválida no | ¿Mecanismo y ciclo de sesión? |
| RF-02 | Móvil: registro | Capturar tipo, fabricante, modelo, versión y sitio; inventario y observaciones opcionales | R, M | ESTABLECIDA | RF-04 | AC-A: envío conserva valores requeridos | ¿Longitudes y catálogos? |
| RF-03 | Móvil: reintento | Conservar formulario al fallar envío y permitir reintento | R | ESTABLECIDA | RF-04, RN-03 | AC-B: recuperar valores y reintentar | ¿Sobrevive cierre de app? |
| RF-04 | API: recepción | Recibir registro de activo; reglas detalladas por definir | R, A | ESTABLECIDA | RN-01, RN-02 | AC-C: válido aceptado, inválido rechazado | ¿Esquema, campos y errores? |
| RF-05 | Persistencia | Guardar registro recibido para consulta | C | ESTABLECIDA | RF-04 | AC-D: lectura devuelve registro confirmado | ¿Transacciones e idempotencia? |
| RF-06 | Web: consulta | Consultar y visualizar activos | R | ESTABLECIDA | RF-05, RN-01 | AC-E: activo confirmado visible | ¿Filtros, paginación? |
| RF-07 | Web: administración | Administración general de activos | R | ESTABLECIDA | RF-06 | Funciones y permisos se definen antes de prueba | ¿Qué operaciones incluye? |
| RF-08 | Web: actualizar/eliminar | Detalle de modificación y eliminación | R, A | PENDIENTE | RF-07 | No habilitar hasta aprobación y prueba | ¿Campos, retención, borrado? |
| RF-09 | CPE: búsqueda | Buscar y presentar candidatos | R | ESTABLECIDA | RN-04 | AC-F: lista o sin resultados diferenciados de error | ¿Paginación y obsoletos? |
| RF-10 | CPE: elección | Persona selecciona candidato | R | ESTABLECIDA | RF-09 | AC-G: sin selección no se consulta CVE | ¿Cambios y trazabilidad? |
| RF-11 | NVD: consulta | Consultar CVE para CPE seleccionado sin espejo completo | R, S | ESTABLECIDA | RF-10, RN-04 | AC-H: respuesta completa o parcial identificada | ¿CPE sin versión, actualización? |
| RF-12 | Hallazgos | Visualizar CVE potenciales hasta verificación y hallazgos | R | ESTABLECIDA | RF-11 | AC-I: fuente y condición potencial visibles | ¿Ciclo y deduplicación? |
| RF-13 | Hallazgos: estado | Modificar estado de un hallazgo | A | PROPUESTA | RF-12, RN-01 | Cambio autorizado y registrado | ¿Estados y transiciones? |
| RF-14 | ZAP: solicitud y consulta | Ejecutar y consultar Baseline para aplicación autorizada | R, M | ESTABLECIDA | RN-02, RN-05 | AC-J/K: solo destino autorizado, ejecución y resultado visibles | ¿Aprobador, expiración y duración? |
| RF-15 | Dashboard | Mostrar resumen general | R | ESTABLECIDA | RF-06, RF-12, RF-14 | Contadores con fuente y momento conocidos | ¿Métricas y actualización? |
| RN-01 | Seguridad: identidad | Autenticación y autorización por recurso | A, S | PENDIENTE | — | Sin permiso no se accede ni modifica | ¿Roles y alcance? |
| RN-02 | Seguridad: datos | Evitar secretos de clientes y credenciales administrativas de equipos | R, S | ESTABLECIDA | — | Validación y revisión de registros evitan almacenamiento previsto de secretos | ¿Controles y gestión de credenciales propias? |
| RN-03 | Disponibilidad: fallos | Manejar fallo de envío preservando formulario | R | ESTABLECIDA | RF-03 | AC-B: error visible sin pérdida | ¿Duración de conservación? |
| RN-04 | Integraciones: fallos | No confundir falla externa con ausencia de resultados | M | PROPUESTA | RF-09, RF-11 | AC-L: fallos identificables, respuesta parcial explícita | ¿Tiempo límite y reintentos? |
| RN-05 | Seguridad: escaneo | Solo destinos web previamente autorizados en lista; aislamiento y sin Active Scan ni dispositivos físicos | R, S, M | ESTABLECIDA | RN-01 | AC-K: solicitudes fuera de alcance bloqueadas también en redirecciones y salida | ¿Controles verificables y lista? |
| RN-06 | Operación | Rendimiento, disponibilidad y accesibilidad medibles | R | PENDIENTE | — | Medir umbrales acordados | ¿Objetivos y entorno? |
| RN-07 | Operación: online-first | No exigir sincronización offline completa | R | ESTABLECIDA | RF-03 | App permite reintentar sin cola de sincronización completa | ¿Borrador tras cierre? |

Las exclusiones y límites de seguridad están en [requisitos](REQUIREMENTS.md); RF-08 no convierte PATCH o DELETE en operaciones aprobadas.
