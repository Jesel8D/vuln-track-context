# Estrategia de pruebas del MVP

**PROPUESTA:** preparar pruebas tras aprobar stack, contratos y entorno. Ninguna prueba de aplicación existe en las copias inspeccionadas; no hay cobertura medible. La tabla describe comportamiento por demostrar, no resultados observados. Las pruebas de seguridad usan datos sintéticos y objetivos de laboratorio autorizados; nunca infraestructura de terceros ni Active Scan. Vincular resultados a [AC-A–L](ACCEPTANCE_CRITERIA.md) y guardar evidencias sin secretos.

| Tipo | Componente / prueba concreta | Resultado verificable |
|---|---|---|
| Unitaria | Validación de campos, normalización, permisos, estados, respuesta NVD y parser de reportes mediante dobles. | Requeridos/optativos coherentes; valores inválidos rechazados; sin CVSS permanece ausente y no se atribuye CVE a ZAP. |
| Contrato API | Backend y clientes: validar esquemas de creación/lectura, encabezados, códigos, errores, paginación e idempotencia acordados. | Request y response corresponden a versión aprobada; datos extra/invalidaciones y errores son consistentes; no asumir PATCH/DELETE. |
| Integración/persistencia | API con PostgreSQL de prueba; crear, leer, confirmar, rollback y fallo de conexión. | Datos correctos tras commit; error antes de commit no produce éxito ni registro parcial; D-002 respetada en ubicación de migraciones. |
| Idempotencia | Dos peticiones concurrentes y pérdida de respuesta tras commit; repetir clave con cuerpo igual y con cuerpo distinto. | Un solo efecto para solicitud idéntica; conflicto para distinta; probar expiración según política aprobada. |
| Autenticación | Inicio de sesión o alternativa aprobada: credencial válida/inválida, sesión expirada/revocada y abuso. | Acceso solo con identidad válida; error genérico y límites según contrato; sin credenciales en logs. |
| Autorización | Dos identidades y recursos ajenos para activos, hallazgos, análisis y dashboard. | Acceso/modificación denegados por recurso; no se filtran datos ni totales fuera del ámbito. |
| Validación / HTTP | Campos ausentes, tipo erróneo, JSON malformado, límites, IDs inexistentes y proveedor no disponible. | Estado y mensaje acordados, sin detalles internos; no se guarda entrada inválida. |
| Móvil / API | Formulario completo y opcionales; red caída antes/durante envío; respuesta perdida y reintento. | Valores conservados y reintento posible; no se muestra confirmación falsa ni se duplica tras commit si se aprueba idempotencia; no se requiere sincronización offline completa. |
| Web / API | Lista/detalle, estado de carga y ausencia de permisos/datos, filtros y dashboard acordados. | El activo guardado aparece por API; errores son visibles y usuarios no ven objetos ajenos. |
| NVD / CPE | Dobles para múltiples candidatos, ninguno adecuado, versión no admitida, cero CVE, página parcial, cuota, timeout y CVSS ausente. | Selección humana; ausencia de CPE distinta de cero CVE y error; resultados con fecha/cobertura y rótulo potencial; reintentos acotados. |
| ZAP controlado | Laboratorio autorizado: ID permitido y rechazado, revocación, redirect, DNS cambiante, IPv4/IPv6, puerto fuera de alcance, timeout y reportes truncados. | Sin conexiones fuera del alcance; control de salida bloquea cada ruta y falla cerrado; solo Baseline; códigos WARN/FAIL distinguidos de fallo operativo. |
| E2E | Móvil → API → PostgreSQL → web; después consulta CPE/NVD con doble y ZAP solo con puerta de seguridad aprobada. | Mismo activo visible de forma autorizada, reintento fiable, CVE potenciales y resultados de análisis controlado demostrados. |
| Regresión y operación | Reejecutar escenarios clave tras cambios, revisión de logs, restauración de datos de prueba y dependencias. | No reaparece comportamiento prohibido ni secretos en evidencias; restauración y límites cumplen criterios aprobados. |

## Ejecución y salida

**PROPUESTA:** pruebas unitarias y de contrato en cada cambio; integración de persistencia en entorno reproducible; E2E al integrar cada hito. Simular errores externos de forma determinista; una prueba con NVD real, si se aprueba, debe respetar su política y cuota, sin depender de ella para la suite básica. Un destino ZAP solo puede usarse con autorización y controles verificables. **PENDIENTE:** herramientas, versiones, criterios cuantitativos, automatización, dispositivos, tiempo máximo y conservación de evidencias; no se asigna porcentaje de cobertura inventado.
