# Plan de implementación: cuatro semanas orientativas

**PROPUESTA:** dos personas, capacidad no validada; priorizar un flujo vertical demostrable. D-002 mantiene bootstrap/SQL en Database y ORM/Alembic en Backend; asignaciones de personas sujetas a acuerdo.

| Semana | Backend | Database | Frontend/móvil y puerta de aceptación |
|---|---|---|---|
| 1 | contrato, autorización y cliente NVD con dobles | acordar selección histórica, FK, constraints, catálogo y migración | formulario existente y mock; decisión dominio/tenencia; datos reales opcionales y versión desconocida definida |
| 2 | búsqueda paginada, selección manual y pruebas | migración en PostgreSQL desechable, pruebas de propiedad y recuperación | seleccionar candidato o dejar pendiente; error NVD visible sin perder activo |
| 3 | ejecutar CVE para selección concreta, límites, paginación, cobertura | persistir ejecución, CVE, trazabilidad y unicidad por ejecución | mostrar potencial/no determinado, revisión y cobertura parcial |
| 4 | pruebas de extremo a extremo, tiempos, reintentos, seguridad | probar actualización desde V1, rollback y migración coordinada | demo inventario → selección → consulta → resultado; verificación dos usuarios y sin fugas |

Si faltan horas, aplazar componentes múltiples, automatización de actualización, dashboard elaborado, colaboración y ZAP; no recortar autorización ni transparencia de cobertura. Tests con respuestas capturadas de tamaño acotado, variantes sin configuración, AND/OR, rangos, versionado, 429, timeout y dos propietarios. No ejecutar consultas masivas.

## Distribución de dos personas y recorte de alcance

Asignación orientativa: persona A lidera FastAPI, cliente externo y autorización; persona B lidera SQL/pruebas de integridad y una interfaz web sencilla. A conserva ORM/Alembic conforme D-002, revisando SQL con B; B revisa datos mostrados y errores con A. No asumir que B puede completar simultáneamente cliente web, móvil nativo y Database: priorizar un cliente de demostración con el contrato reutilizable, y decidir si móvil se entrega después. La distribución previa A/B era propuesta, no asignación aprobada de habilidades.

Hito 1: registrar activo propio sin CPE y obtenerlo de nuevo; usuario B recibe 404. Hito 2: búsqueda con fixture y luego llamada real acotada, selección humana y registro de UUID/nombre/fecha; ningún candidato se selecciona automáticamente. Hito 3: consulta CVE y pantalla que distingue cero resultados, parcial y error; historial anterior no cambia al seleccionar otro CPE. Hito 4: demo repetible sin clave privada ni escáner; reintentos no duplican efectos y no se mezclan datos de propietarios. El alcance mínimo no necesita evaluar automáticamente toda lógica de aplicabilidad: debe conservarla y comunicar revisión pendiente sin afirmar certeza.
