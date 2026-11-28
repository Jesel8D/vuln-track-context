# Decisiones y preguntas abiertas

## Decisiones formalmente aprobadas

### D-001 — Repositorio central de documentación

**Estado: APROBADA.** Se utilizará el repositorio independiente `vuln-track-context` para documentación general, decisiones, requisitos, arquitectura e instrucciones compartidas. Justificación histórica: No se dispone de una justificación original registrada.

### D-002 — Responsabilidad del repositorio de base de datos

**Estado: APROBADA.** `database` se encarga de configuración de PostgreSQL, scripts de inicialización, datos de demostración o seed y documentación propia. Los modelos SQLAlchemy y migraciones Alembic pertenecen a `backend`. Justificación histórica: No se dispone de una justificación original registrada.

### D-003 — Alcance web únicamente

**Estado: APROBADA por instrucción explícita del usuario solicitante el 2026-11-27.** VulnTrack será solo web; no se desarrollará una aplicación móvil dentro del proyecto. Fuente: mensaje de este chat, «descarta lo movil, solo quedará en web». Esta decisión reemplaza la prioridad móvil descrita en documentos previos; no aprueba por sí misma el despliegue público ni habilita ZAP.

## Propuestas técnicas

**ESTABLECIDA:** el stack implementado consta de cliente web React + Vite + TypeScript, API Python + FastAPI y PostgreSQL con SQLAlchemy/Alembic en backend. El navegador se conecta a la API, no a PostgreSQL. Docker Compose está preparado para los entornos documentados. Las funciones y limitaciones vigentes se describen en los README de cada repositorio.

## Preguntas abiertas y autorización humana

- **PENDIENTE:** ratificar con el segundo desarrollador el alcance y los detalles técnicos del MVP, sin alterar D-003 aprobada por el usuario solicitante.
- **PENDIENTE:** ¿Qué alcance y reglas tendrán la actualización detallada y la eliminación de activos?
- **PENDIENTE:** ¿Cuáles serán los atributos, tipos, relaciones, cardinalidades, restricciones, índices, integridad e identificación del modelo?
- **PENDIENTE:** ¿Cuáles serán los contratos completos, autenticación, autorización, roles, validaciones, errores, paginación, códigos HTTP y reglas de cada endpoint?
- **PENDIENTE:** autorización de objetivos, aislamiento y controles de ZAP, porque ZAP no forma parte del MVP actual. Reabrir solo ante aprobación explícita de una fase posterior.
- **PENDIENTE:** ¿Se aprueban la distribución semanal, las responsabilidades y los criterios de aceptación?
- **PENDIENTE:** cualquier incorporación de una funcionalidad excluida del MVP requiere aprobación explícita.

Las decisiones D-001, D-002 y D-003 están formalmente registradas; cualquier cambio posterior requiere autorización humana.

## Preguntas adicionales de la auditoría

- **PENDIENTE:** ¿Qué objetivos medibles de rendimiento, disponibilidad y accesibilidad se requieren para la aplicación web?
- **PENDIENTE:** ¿Cuál será la vigencia y el comportamiento de las claves de idempotencia, especialmente ante respuestas perdidas?
- **PENDIENTE:** ¿Qué hacer cuando no existe un CPE adecuado o su versión no permite el filtro de NVD? ¿Cómo presentar resultados parciales o desactualizados?
- **PENDIENTE:** ¿Qué política de sesiones, permisos por recurso y retención de registros se aprueba? ¿Cómo se gestionan los secretos operativos propios sin almacenar secretos de clientes?
- **PENDIENTE:** ¿Quién autoriza, modifica y revoca destinos de prueba? ¿Qué mecanismos de salida por conexión, DNS y redirecciones se pueden probar antes de ejecutar ZAP?
- **PENDIENTE:** ¿Qué estados y transiciones tendrán las ejecuciones y hallazgos, y cómo se conserva la evidencia histórica?
- **PENDIENTE:** ¿Cómo se calcularán las métricas del dashboard y la deduplicación, y qué versiones y fuentes de CVSS se mostrarán?
- **PENDIENTE:** ¿Qué entorno, presupuesto y criterios cuantitativos de finalización tendrá el MVP?

## Paquete de decisiones preparado

Los documentos de propuesta heredados conservan su texto como antecedente. Las recomendaciones de ubicación y tecnología móvil quedan fuera del alcance por D-003; revisar solo las propuestas web, identidad y claves idempotentes antes de tratarlas como decisión.

## Database V1 provisional

**PROPUESTA:** el [seguimiento Database V1](DATABASE_V1_PROGRESS.md) registra las elecciones mínimas materializadas y probadas bajo autorización de la misión actual. **PENDIENTE:** aprobar el contrato/esquema global, normalización de usernames, login operativo y permisos por recurso, política de retención y claves idempotentes; planificar evolución segura y conciliación de baseline con migraciones backend si se adopta ese stack. ZAP continúa pendiente y se rechaza en el esquema V1. No se incorporan nuevas decisiones formalmente aprobadas.

## Registro parcial del MVP — 2026-11-08

La [aprobación individual de Jesel](MVP_APPROVAL_RECORD.md) registra MVP-001–MVP-013 y diferencia conformidad, propuesta y directriz de preservación. La ratificación técnica de Rudi permanece PENDIENTE; D-003 registra por separado el alcance web-only instruido el 2026-11-27. ZAP sigue fuera del MVP; las decisiones D-001–D-003 constan en `context/decisions.json`.
