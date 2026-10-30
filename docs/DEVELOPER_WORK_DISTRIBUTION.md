# Distribución de trabajo propuesta

**PROPUESTA:** dos desarrolladores A y B sin atribuir habilidades ni nombres. Duración aproximada de cuatro semanas, no compromiso. La ubicación del móvil, stack, permisos, contratos y entorno están **PENDIENTE**. El plan completo y sus puertas están en [IMPLEMENTATION_MASTER_PLAN.md](IMPLEMENTATION_MASTER_PLAN.md).

## Cronograma y revisión cruzada

| Semana | Desarrollador A | Desarrollador B | Coordinación, entrega demostrable y revisión |
|---|---|---|---|
| 1 | Proponer esquema de activos, identidad/permisos e idempotencia; tras G0/G1 preparar backend/database. | Proponer flujos y contrato de cliente móvil/web; preparar pruebas y entorno tras G0; estudiar viabilidad de aislamiento ZAP con A. | A y B acuerdan payloads, códigos, claves y acceso antes de paralelizar. Entrega: contrato/modelo aprobados y entornos reproducibles; cada uno revisa el diseño del otro. |
| 2 | API de creación/consulta, transacciones y pruebas de persistencia en backend; coordinación de configuración database. | Formulario móvil y consulta web contra dobles y luego API; conservar datos al fallar. | Integración diaria; entrega demostrable: móvil → API → PostgreSQL → web, con fallo/reintento sin duplicación. B revisa contrato y errores backend; A revisa clientes y manejo de sesión. |
| 3 | Cliente NVD, cuotas, paginación y almacenamiento de hallazgos en backend. | Búsqueda/selección CPE, estados de carga/error y lista de resultados en frontend; datos simulados mientras se integra. | Acuerdan formato CPE/resultados y cobertura; entrega: selección humana y CVE potenciales con error diferenciado. B revisa respuestas NVD; A revisa la presentación y pruebas. |
| 4 | Ejecutor Baseline solo si G3 y pruebas de salida están aprobadas; pruebas de integración backend. | Consulta de ejecuciones/hallazgos, dashboard y pruebas E2E, coordinando evidencias y dispositivo móvil. | Entrega: análisis controlado en laboratorio autorizado, dashboard y demostración final; revisión cruzada de seguridad, permisos y pruebas. Si G3 falla, no ejecutar y reportar MVP incompleto. |

## Dependencias y puntos de integración

1. **Antes del trabajo paralelo:** acordar semántica del formulario, campos opcionales, IDs, identidad, permisos por recurso, códigos de error, paginación e idempotencia. Usar el [borrador API](API_CONTRACT_DRAFT.md) solo tras aprobar las partes necesarias.
2. **Punto I1:** A y B integran móvil, API, PostgreSQL y web con backend real; previamente B puede utilizar respuestas simuladas. Ambas personas verifican rechazo y errores.
3. **Punto I2:** A y B prueban juntos persistencia y recuperación tras pérdida de respuesta. La clave se reutiliza solo para la misma solicitud y su comportamiento depende de política aprobada.
4. **Punto I3:** revisar juntos fuente, cobertura parcial, semántica de CPE y rótulo potencial; B valida experiencia ante ausencia de candidato, cero CVE y fallo.
5. **Punto I4:** otra persona revisa la autorización y las barreras ZAP; la demostración requiere evidencias de rechazo por redirección, DNS y conexiones fuera de alcance.

## Conflictos y carga

Ambos pueden tocar contratos compartidos, modelos de activo y hallazgos, configuración de entornos, dependencias o pruebas E2E. Acordar primero el contrato y un responsable temporal por cambio; usar ramas independientes y revisión del otro antes de integrar. No se ha definido el flujo de ramas ni una estructura de carpetas para implementación: ambas decisiones requieren aprobación. Asignar tareas móviles/web a B no excluye que A ayude en pruebas; A y B ajustan carga según complejidad real sin suponer experiencia. **PENDIENTE:** disponibilidad, dispositivos, experiencia, estimaciones de cada tarea y criterio de cierre.
