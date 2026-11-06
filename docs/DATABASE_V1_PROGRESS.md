# Database V1: construcción y evidencia

Fecha: 30-10-2026. La misión de integración y Database V1 autorizó integrar directamente ambas ramas en `dev/jesel` y construir PostgreSQL provisional en `database`. No se cambiaron D-001, D-002 ni sus justificaciones. Las propuestas previas se conservaron; implementación y prueba no equivalen a aprobación global del stack/modelo.

## Integración documental

Se integraron `9a09af4` (plan maestro) y `f718965` (paquete de decisiones) en ese orden; el segundo mensaje real es `docs: prepare technical decision package`, aunque la misión lo describía con otro texto. El conflicto del índice README se resolvió conservando ambos bloques y actualizando el aviso obsoleto de integración. Commit de integración: `74aa492`. Ambos commits originales son ancestros y se publicó directamente `dev/jesel`; no se abrió PR ni se tocó main.

## Auditoría antes del SQL

Los documentos solicitados y sus nombres reales estaban disponibles tras consolidar, junto con los diagramas. Información suficiente: campos de activos y opcionales, UUID propuesto, selección humana CPE, hallazgos potenciales, relaciones conceptuales propuestas y reparto D-002. Supuestos mínimos: esquema `vulntrack`, username ASCII único, estados acotados, FKs restrictivas, timestamps de auditoría y tabla técnica de idempotencia, todos **PROPUESTA** implementada para ensayo. Riesgos: contrato/modelo no formalmente aprobados, control de acceso incompleto y datos sensibles en notas; no se trasladan automáticamente a requisitos aceptados.

## Implementado y probado

Rama de implementación: [`feat/postgresql-database-v1`](https://github.com/Jesel8D/vuln-track-database/tree/feat/postgresql-database-v1). Archivos:

- `.env.example`, `.gitignore`, `compose.yaml` e `init/00-bootstrap.sh`.
- `sql/001_schema.sql`, `sql/002_privileges.sql`, `sql/003_queries.sql`.
- `seed/001_demo.sql`, `tests/001_integrity.sql`, `tests/run.sh`.
- `README.md`, `docs/SCHEMA.md`, `docs/PROVISIONAL_DECISIONS.md`, `docs/TEST_RESULTS.md`, `diagrams/database_v1.mmd`.

Commits publicados en esa rama: `f443b85` (esquema y entorno local) y `b493fd1` (pruebas y documentación). Se publicó por SSH sin cambiar el remoto HTTPS configurado ni crear una rama main o un PR.

Tablas: `users`, `assets`, `analysis_runs`, `findings` y `asset_registration_requests` (soporte técnico). FKs User/Asset → AnalysisRun → Finding y User/Asset → solicitudes; sin cascadas. Campos opcionales, restricciones, índices justificados, timestamps y grupo técnico NOLOGIN implementados. Consultas y seed sintético reproducibles.

PostgreSQL real **16.15**, imagen fija con digest; configuración de versión **PROPUESTA** provisional, no aprobación del stack completo. Suite final con salida 0: 55 comprobaciones SQL (20 positivas/35 negativas), autenticación TCP correcta/incorrecta, enlace solo localhost, dos transacciones concurrentes con una sola alta confirmada, persistencia tras reinicio y recreación usando el mismo volumen. El runner usó un proyecto nuevo y detuvo solo su contenedor; conservó volúmenes de prueba. Véanse [resultados](https://github.com/Jesel8D/vuln-track-database/blob/feat/postgresql-database-v1/docs/TEST_RESULTS.md) y [diccionario implementado](https://github.com/Jesel8D/vuln-track-database/blob/feat/postgresql-database-v1/docs/SCHEMA.md).

## Implementado pero no probado exhaustivamente

Sin prueba de carga, recuperación de backup, portabilidad de arquitecturas ni renderizado Mermaid. Los checks de CPE/CVSS/hash son de formato mínimo y no validación semántica o criptográfica completa. El esquema no registra cada transición histórica ni aplica permisos por objeto. El dueño de desarrollo es administrador local; la API no debe usarlo.

## Propuesto y pendiente

**PROPUESTA:** decisiones DBV1-01 a DBV1-08 en el [registro provisional de database](https://github.com/Jesel8D/vuln-track-database/blob/feat/postgresql-database-v1/docs/PROVISIONAL_DECISIONS.md). La tabla idempotente y unicidad están probadas; **PENDIENTE** cálculo/comparación de huella, autorización, respuesta/reconciliación/expiración y garantía de idempotencia E2E del backend. Se conserva la distinción entre sin evaluación, consulta completa sin hallazgos, parcial y fallo; la ausencia de un CPE adecuado se resolverá antes de crear la consulta, no se interpreta como cero CVE.

ZAP queda **PENDIENTE**, rechazado por el esquema V1 hasta diseñar catálogo autorizado y controles DNS/redirecciones/egress. No hay integración NVD real, API, autenticación funcional, web ni móvil. No se modificaron backend/frontend ni se generaron modelos ORM o migraciones Alembic. El diagrama original `diagrams/data_model.mmd` sigue intacto.

Próximo paso **PROPUESTA:** revisar/aprobar el contrato mínimo, identidad y esquema provisional; seleccionar stack backend; crear login operativo con secretos externos y conectar creación/consulta de activos en transacción. Si se adopta SQLAlchemy/Alembic, el baseline deberá conciliarse con migraciones dentro de backend respetando D-002, sin reaplicar ciegamente los scripts sobre datos existentes.

## Revisión posterior: propuesta Database V2

Fecha: 05-11-2026. Tras publicar Database V1, la rama [`rudi`](https://github.com/Jesel8D/vuln-track-database/tree/rudi) agregó una **propuesta aditiva** para aplicaciones web y workspaces. Commits: `66b68aa` (modelo V2) y `54b7644` (RLS y contexto HMAC). El backend documenta la integración HMAC en su rama [`rudi`](https://github.com/Jesel8D/vuln-track-backend/tree/rudi), commit `1a7ec7b`.

La propuesta agrega `workspaces`, `workspace_members`, `applications`, `application_targets`, `technologies`, ejecuciones y hallazgos para aplicaciones, recomendaciones, metadatos de imports, auditoría append-only y RLS con contexto firmado de corta duración. Conserva las tablas V1 y no transforma ni reasigna activos históricos. El contexto describe cómo la base valida HMAC, pero el backend aún no tiene una implementación de autenticación, firma ni establecimiento del contexto.

**No verificado:** la migración `sql/004_v2_application_model.sql` y `tests/002_v2_security.sql` se añadieron al repositorio database, pero no se ejecutaron contra PostgreSQL. Docker no estaba disponible en la sesión. No afirmar que la migración o RLS pasaron sus pruebas hasta ejecutarlas.

### Contradicciones y aprobaciones pendientes

- El alcance **ESTABLECIDA** de [requisitos](REQUIREMENTS.md) y el modelo V1 describen activos con tipo de dispositivo, fabricante, modelo, versión y sitio. El objetivo de V2 describe aplicaciones web, URL, tecnologías y versiones. La migración conserva ambos modelos sin resolver si VulnTrack gestionará dispositivos, aplicaciones web o ambos; requiere decisión humana antes de hacer V2 el modelo activo.
- [Identidad y permisos](IDENTITY_AND_ACCESS_PROPOSAL.md) dice que no se inventa una estructura multitenant y que permisos/roles siguen **PENDIENTE**. El workspace de V2 es una alternativa técnica **PROPUESTA**, no aprobación de multitenencia.
- D-002 asigna modelos SQLAlchemy y migraciones Alembic a backend si se adopta ese stack. V2 incorpora un script SQL aditivo en database. Confirmar cómo encaja con D-002 y con la estrategia de migraciones antes de operar sobre datos existentes.
- La suite nueva incluye pruebas negativas RLS/HMAC, pero requiere PostgreSQL real, revisión del protocolo de firma entre API y base, y tratamiento seguro de la clave fuera de Git. La firma no implementa por sí sola autenticación de usuarios.

No se cambian requisitos, decisiones JSON ni decisiones aprobadas en esta actualización; se registra estado real de ramas y pendientes para revisión.
