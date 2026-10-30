# Matriz para autorización humana

Todas las filas están **PENDIENTE**; sus recomendaciones son **PROPUESTA**. D-001 y D-002 permanecen **APROBADA** y no se reabren en esta matriz. Responder por identificador con «aprobar», «rechazar» o una alternativa; una recomendación no se convierte en aprobación por silencio. Una aprobación debe registrarse de forma explícita por una persona autorizada antes de aplicar cambios de estado en las decisiones centrales.

Cada fila expresa situación, alternativas y recomendación; la columna de consecuencia incluye impacto técnico, riesgo de no decidir y dependencias. Ninguna casilla está marcada de antemano.

## Bloqueantes para comenzar desarrollo

| ID / tema | Situación y alternativas | Recomendación / justificación | Impacto, riesgo y dependencias | Estado / autorización |
|---|---|---|---|---|
| P-01 Stack backend | Sin tecnología aprobada; FastAPI/Python o Express/TypeScript. | FastAPI/Python si equipo lo domina; reduce trabajo inicial de OpenAPI. Express si experiencia TS es mayor. | Define dependencias y pruebas; sin elección no preparar backend. Depende de experiencia real y P-02. | PENDIENTE / aprobación humana |
| P-02 Versiones y entorno | Sin matriz validada; fijar versiones probadas o posponer selección. | Elegir versiones soportadas y verificar conjunto antes de fijarlas, sin números no comprobados. | Evita incompatibilidad; bloqueo de CI/entorno; depende de P-01 y plataformas. | PENDIENTE / aprobación humana |
| P-03 Ubicación móvil | Sin ubicación aprobada; frontend, repositorio separado o monorepo. | Dos aplicaciones separadas en frontend; reduce administración para dos personas. | Cambia organización y CI; sin decisión no crear proyecto. Depende de permisos y [comparativa](MOBILE_REPOSITORY_STRATEGY.md). | PENDIENTE / aprobación humana |
| P-04 Base/ORM | PostgreSQL contemplado, adopción propuesta; SQLAlchemy/Alembic o ORM alternativo. | PostgreSQL y SQLAlchemy/Alembic si P-01 Python. | Define persistencia; no hay entorno ni migración. Respeta D-002; alternativa Node y su ubicación deben aclararse. | PENDIENTE / aprobación humana |
| P-05 Identidad/permisos | Identidad local o proveedor; roles y límites no definidos. | Sesiones revocables e identidad local solo si no hay proveedor institucional; roles mínimos por recurso. | Bloquea protección de API/clientes; requiere P-01 y administración de usuarios. Ver [propuesta](IDENTITY_AND_ACCESS_PROPOSAL.md). | PENDIENTE / aprobación humana |

## Antes de completar el primer flujo

| ID / tema | Situación y alternativas | Recomendación / justificación | Impacto, riesgo y dependencias | Estado / autorización |
|---|---|---|---|---|
| P-06 Campos y contrato | Campos contemplados; obligatoriedad, longitudes, IDs y errores sin definir. | Aprobar [contrato de activos](ASSET_CONTRACT_PROPOSAL.md), sujeto a ajustes. | Sin contrato habrá retrabajo cliente/API; depende de P-01/P-05 y modelo. | PENDIENTE / aprobación humana |
| P-07 Modelo y relaciones | Cuatro entidades conceptuales sin atributos ni relaciones aprobados. | Aprobar primero Asset/User mínimos y revisar AnalysisRun/Finding antes de sus integraciones. | Define persistencia y migraciones; riesgo de integridad e historial. Depende de P-04/P-06. | PENDIENTE / aprobación humana |
| P-08 Idempotencia | Reintento con respuesta perdida puede duplicar; clave transaccional o reconciliación manual. | Clave por actor/ruta/cuerpo, transacción única; 24 h es ejemplo negociable. | Requiere almacenamiento y manejo de expiración; sin acuerdo riesgo de duplicados. Depende de P-05/P-06/P-07. | PENDIENTE / aprobación humana |
| P-09 Formulario local | Conservar ante fallo establecido; duración y cierre app no definidos. | Retener durante sesión y aprobar almacenamiento seguro si se exige recuperación tras cierre. | Cambia UX y riesgo de exposición; no implica sincronización offline completa. Depende de P-03 y plataforma. | PENDIENTE / aprobación humana |
| P-10 Dashboard y consultas | Resumen establecido; métricas, paginación y visibilidad no definidas. | Definir conteos mínimos con fecha y permisos; offset propuesto para activos. | Evita cifras engañosas y fuga de datos; depende de P-05/P-07. | PENDIENTE / aprobación humana |

## Antes de integrar NVD

| ID / tema | Situación y alternativas | Recomendación / justificación | Impacto, riesgo y dependencias | Estado / autorización |
|---|---|---|---|---|
| P-11 Selección y cobertura | Persona debe seleccionar CPE; ausencia de candidato y parcialidad no definidas. | Mostrar sin candidato, cero CVE, error y parcial por separado; no automatizar selección. | Evita falsa seguridad; depende de contrato y verificación de API oficial. | PENDIENTE / aprobación humana |
| P-12 NVD y CVSS | Cuotas/esquemas oficiales no legibles aquí; caché y selección de CVSS indefinidas. | Verificar fuente oficial y limitar consultas; caché acotada opcional sin Redis; preservar fuente/versión. | Sin política hay abuso o datos obsoletos; depende de P-11 y disponibilidad NVD. | PENDIENTE / aprobación humana |

## Antes de habilitar ZAP

| ID / tema | Situación y alternativas | Recomendación / justificación | Impacto, riesgo y dependencias | Estado / autorización |
|---|---|---|---|---|
| P-13 Destinos y autoridad | Lista autorizada exigida; gestor, alcance y revocación no definidos. | Autoridad humana registra destino exacto y vigencia; usuario selecciona ID, nunca URL libre. | Sin ello hay abuso y escaneo no autorizado. Depende de P-05 y documentación de laboratorio. | PENDIENTE / aprobación humana |
| P-14 Aislamiento y salida | Docker propuesto, no hay pruebas de red o barrera DNS/redirect. | Denegar salida por defecto y comprobar cada conexión; bloquear ejecución si no se demuestra. | Puede retrasar MVP; evita SSRF y acceso fuera de alcance. Depende de P-13 y entorno de pruebas autorizado. | PENDIENTE / aprobación humana |
| P-15 Resultados y auditoría | Alertas y acceso contemplados; retención/evidencia no definidas. | Guardar datos mínimos, permiso por ejecución y registro sin secretos. | Riesgo de filtración o ausencia de trazabilidad; depende de P-05/P-07/P-13. | PENDIENTE / aprobación humana |

## Diferibles para versión posterior

| ID / tema | Situación y alternativas | Recomendación / justificación | Impacto, riesgo y dependencias | Estado / autorización |
|---|---|---|---|---|
| P-16 Actualizar/eliminar activos | Administración general establecida; detalles no definidos. | Mantener operaciones sin habilitar hasta acordar campos, borrado e historial. | Evita pérdida de datos; limita administración. Depende de P-05/P-07. | PENDIENTE / aprobación humana |
| P-17 Mejoras excluidas | QR, GPS, Redis, PDF, KEV, sincronización offline completa y Active Scan excluidos del MVP. | No incorporarlos sin aprobación explícita y revisión de alcance. | Evita ampliar cuatro semanas; depende de nueva decisión humana. | PENDIENTE / aprobación humana |

Las prioridades son una sugerencia de orden y no reducen el alcance establecido. Si ZAP seguro no puede habilitarse, el MVP permanece incompleto hasta que la persona responsable decida el cambio de plazo o alcance.
