# Decisiones y preguntas abiertas

## Decisiones formalmente aprobadas

### D-001 — Repositorio central de documentación

**Estado: APROBADA.** Se utilizará el repositorio independiente `vuln-track-context` para documentación general, decisiones, requisitos, arquitectura e instrucciones compartidas. Justificación histórica: No se dispone de una justificación original registrada.

### D-002 — Responsabilidad del repositorio de base de datos

**Estado: APROBADA.** `database` se encarga de configuración de PostgreSQL, scripts de inicialización, datos de demostración o seed y documentación propia. Los modelos SQLAlchemy y migraciones Alembic pertenecen a `backend`. Justificación histórica: No se dispone de una justificación original registrada.

## Propuestas técnicas

**PROPUESTA:** arquitectura cliente-servidor modular; React + Vite + TypeScript para web; React Native + Expo + TypeScript para móvil; Python + FastAPI para backend; PostgreSQL, SQLAlchemy y Alembic para persistencia; Docker para aislamiento. Los endpoints de [API](API_CONTRACT.md), las entidades del [modelo](DATA_MODEL.md) y la distribución del [plan](IMPLEMENTATION_PLAN.md) también son propuestas. D-002 fija responsabilidades de repositorio sin aprobar por sí sola la adopción de todas esas tecnologías.

## Preguntas abiertas y autorización humana

- **PENDIENTE:** ¿Se aprueban las tecnologías y la arquitectura propuestas, incluido el mecanismo de aislamiento?
- **PENDIENTE:** ¿Qué alcance y reglas tendrán la actualización detallada y la eliminación de activos?
- **PENDIENTE:** ¿Cuáles serán los atributos, tipos, relaciones, cardinalidades, restricciones, índices, integridad e identificación del modelo?
- **PENDIENTE:** ¿Cuáles serán los contratos completos, autenticación, autorización, roles, validaciones, errores, paginación, códigos HTTP y reglas de cada endpoint?
- **PENDIENTE:** ¿Cómo se administrarán y verificarán los objetivos autorizados, el aislamiento y los controles de ejecución de ZAP?
- **PENDIENTE:** ¿Se aprueban la distribución semanal, las responsabilidades y los criterios de aceptación?
- **PENDIENTE:** cualquier incorporación de una funcionalidad excluida del MVP requiere aprobación explícita.

Solo D-001 y D-002 tienen aprobación formal registrada; las propuestas y cambios a decisiones aprobadas requieren autorización humana antes de tratarse como decisiones aprobadas.
