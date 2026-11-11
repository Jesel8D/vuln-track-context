# Decisiones y preguntas abiertas

## Decisiones formalmente aprobadas

### D-001 — Repositorio central de documentación

**Estado: APROBADA.** Se utilizará el repositorio independiente `vuln-track-context` para documentación general, decisiones, requisitos, arquitectura e instrucciones compartidas. Justificación histórica: No se dispone de una justificación original registrada.

### D-002 — Responsabilidad del repositorio de base de datos

**Estado: APROBADA.** `database` se encarga de configuración de PostgreSQL, scripts de inicialización, datos de demostración o seed y documentación propia. Los modelos SQLAlchemy y migraciones Alembic pertenecen a `backend`. Justificación histórica: No se dispone de una justificación original registrada.

### D-003 — Conteo de hallazgos en el dashboard

**Estado: APROBADA.** El dashboard toma el análisis más reciente por tecnología y cuenta cada CVE una vez dentro de esa tecnología. La misma CVE puede contarse de nuevo en otra tecnología. El backend interpreta “más reciente” como el análisis completado más reciente y presenta las ejecuciones en curso o fallidas en contadores separados. La selección de análisis completado es una interpretación de implementación para evitar mezclar resultados incompletos; no cambia la regla aprobada de conteo. Fuente: respuesta explícita del usuario en la conversación de trabajo del 2026-11-10.

## Propuestas técnicas

**PROPUESTA:** arquitectura cliente-servidor modular; React + Vite + TypeScript para web; React Native + Expo + TypeScript para móvil; Python + FastAPI para backend; PostgreSQL, SQLAlchemy y Alembic para persistencia; Docker para aislamiento. Los endpoints de [API](API_CONTRACT.md), las entidades del [modelo](DATA_MODEL.md) y la distribución del [plan](IMPLEMENTATION_PLAN.md) también son propuestas. D-002 fija responsabilidades de repositorio sin aprobar por sí sola la adopción de todas esas tecnologías.

## Preguntas abiertas y autorización humana

- **PENDIENTE:** ¿Se aprueban las tecnologías y la arquitectura propuestas, incluido el mecanismo de aislamiento?
- **PENDIENTE:** ¿Qué alcance y reglas tendrán la actualización detallada y la eliminación de activos?
- **PENDIENTE:** ¿Cuál es la cantidad máxima de activos por usuario/workspace y qué política de retención aplica? Los límites de paginación de las API no son una cuota de activos.
- **PENDIENTE:** ¿Cuáles serán los atributos, tipos, relaciones, cardinalidades, restricciones, índices, integridad e identificación del modelo?
- **PENDIENTE:** ¿Cuáles serán los contratos completos, autenticación, autorización, roles, validaciones, errores, paginación, códigos HTTP y reglas de cada endpoint?
- **PENDIENTE:** ¿Cómo se administrarán y verificarán los objetivos autorizados, el aislamiento y los controles de ejecución de ZAP?
- **PENDIENTE:** ¿Se aprueban la distribución semanal, las responsabilidades y los criterios de aceptación?
- **PENDIENTE:** cualquier incorporación de una funcionalidad excluida del MVP requiere aprobación explícita.

Solo D-001 a D-003 tienen aprobación formal registrada; las propuestas y cambios a decisiones aprobadas requieren autorización humana antes de tratarse como decisiones aprobadas.

## Preguntas adicionales de la auditoría

- **PENDIENTE:** ¿La conservación del formulario debe sobrevivir al cierre de la aplicación y cómo se protegerá localmente?
- **PENDIENTE:** ¿Cuál será la vigencia y el comportamiento de las claves de idempotencia, especialmente ante respuestas perdidas?
- **PENDIENTE:** ¿Qué hacer cuando no existe un CPE adecuado o su versión no permite el filtro de NVD? ¿Cómo presentar resultados parciales o desactualizados?
- **PENDIENTE:** ¿Qué política de sesiones, permisos por recurso y retención de registros se aprueba? ¿Cómo se gestionan los secretos operativos propios sin almacenar secretos de clientes?
- **PENDIENTE:** ¿Quién autoriza, modifica y revoca destinos de prueba? ¿Qué mecanismos de salida por conexión, DNS y redirecciones se pueden probar antes de ejecutar ZAP?
- **PENDIENTE:** ¿Qué estados y transiciones tendrán las ejecuciones y hallazgos, y cómo se conserva la evidencia histórica?
- **PENDIENTE:** ¿Qué versiones y fuentes de CVSS se mostrarán y cómo se indicará la vigencia de esos datos?
- **PENDIENTE:** ¿Qué plataformas, versiones, entornos, presupuesto y criterios cuantitativos de finalización tendrá el MVP?

## Paquete de decisiones preparado

**PENDIENTE:** revisar individualmente las alternativas y recomendaciones de [DECISION_APPROVAL_CHECKLIST.md](DECISION_APPROVAL_CHECKLIST.md), incluida la ubicación de la aplicación móvil, la matriz de versiones compatibles, el modelo de identidad, la política de claves idempotentes. La rama del plan maestro y la del paquete se integraron en `dev/jesel` el 30-10-2026; esa integración documental no aprueba sus propuestas técnicas. Las recomendaciones de ese paquete no alteran D-001 ni D-002 ni constituyen autorización humana.

## Database V1 provisional

**PROPUESTA:** el [seguimiento Database V1](DATABASE_V1_PROGRESS.md) registra las elecciones mínimas materializadas y probadas bajo autorización de la misión actual. **PENDIENTE:** aprobar el contrato/esquema global, normalización de usernames, login operativo y permisos por recurso, política de retención y claves idempotentes; planificar evolución segura y conciliación de baseline con migraciones backend si se adopta ese stack. ZAP continúa pendiente y se rechaza en el esquema V1. No se incorporan nuevas decisiones formalmente aprobadas.

## Revisión de la propuesta Database V2

- **PENDIENTE:** resolver si el dominio de VulnTrack es el inventario de dispositivos ya establecido, la gestión de aplicaciones web de la conversación de diseño, o ambos. Database V2 propone aplicaciones web; no modifica ni reemplaza RF-02 por sí sola.
- **PENDIENTE:** decidir si se aprueba el modelo de workspaces y membresías multiusuario propuesto en Database V2. La propuesta no equivale a aprobación del modelo multitenant ni de roles.
- **PENDIENTE:** confirmar cómo se aplica D-002 a `sql/004_v2_application_model.sql` y a futuras migraciones: SQL de evolución en database frente a modelos/migraciones Alembic en backend si se adopta SQLAlchemy/Alembic.
- **PENDIENTE:** revisar el contexto HMAC, la custodia/rotación de la clave, el contrato de transacción y la autenticación/autorización que el backend todavía debe implementar.
- **PENDIENTE:** ejecutar y revisar la migración V2 y sus pruebas contra PostgreSQL antes de presentar sus controles como verificados.
