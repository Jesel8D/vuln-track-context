# Compatibilidad entre implementaciones

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

| Componente | Estado real | Clasificación | Adaptación mínima y evidencia |
|---|---|---|---|
| Login/JWT/usuario activo | implementado Backend V1.3 | Compatible sin cambios | reutilizar current_user; probar nuevos endpoints |
| Creación y lectura inventario | versión string requerida | Compatible con adaptación / Requiere migración | version nullable en SQL/ORM/Pydantic y clientes; snapshots antiguos siguen válidos |
| Propiedad | filtro created_by en backend, 0002 propuesto; ausente V1 SQL | Requiere decisión / Requiere migración | aprobar propiedad y coordinar 0002; no backfill inventado |
| Catálogo CPE | no endpoints actuales | Compatible con adaptación | nuevo cliente NVD, parser, caché/límites; historial nuevo |
| Idempotencia activos | PK actor/método/ruta/clave, CHECK /assets | Compatible sin cambios para registro | no cambiar registros previos ni usar para otros efectos |
| Idempotencia nuevos POST | no existe | Requiere migración | claves/huella por fila de evento/ejecución y UNIQUE |
| analysis_runs | CPE snapshot, estado y coverage existentes | Compatible con adaptación / Requiere migración | añadir identificación, counters/metadatos, actor/clave; conservar estados V1 |
| findings | potential y open/reviewed, CVSS jsonb | Requiere migración | CHECK undetermined/not_applicable, unicidad y evidencia/revisión |
| Pydantic | strict, extra forbid y NFC | Compatible con adaptación | version nullable; modelos nuevos explícitos; no aceptar propietario |
| SQLAlchemy/Alembic | ORM y verificador de catálogo estrictos | No verificado en esquema propuesto | ampliar manifiesto de catálogo y migraciones backend; sin stamp ciego |
| V1 privilegios e índices | grants estrechos y cinco tablas | Requiere migración | nuevos grants por columna sin DDL/DELETE; UNIQUE e índices nuevos |
| V1 seed | activos sin dueño, usuario inactivo | Compatible como legado | mantener sin apropiación; nuevos datos ficticios propios para demo |
| V2 workspaces/membresías | SQL adicional y RLS | Requiere decisión; incompatible con propiedad sin rediseño | posponer 004; no afirmar protección de V1 |
| V2 applications/targets | web y URL autorizada | Incompatible con inventario genérico sin adaptación | posponer; no convertir modelo en URL |
| V2 technologies | componentes ligados a application/workspace | Compatible conceptualmente, requiere rediseño para assets | aprovechar conceptos, no copiar FK |
| V2 application_analysis_runs/findings | duplican estructura para aplicaciones | No integrar en camino MVP | reutilizar V1; no dos fuentes para mismos hallazgos |
| V2 HMAC/RLS | validación parcial en rama auditoría | No verificado para integración | sin dependencia del MVP; no FORCE en origen, V1 sin RLS |
| HTTP CORS | GET/POST autorizados | Compatible con adaptación | habilitar PATCH de revisión en allow-methods y probar preflight; otros headers preservados |
| Frontend | main sin commits | requiere implementación | formulario, selección, listados/resultados; no asumir React existente |
| Contexto D-001/D-002 | aprobadas | Compatible sin cambios | contexto independiente; Rudi SQL y Jesel ORM/Alembic |
| Contexto ZAP/móvil | alcance establecido anterior | Requiere decisión | resolver CT-01/CT-02, no declarar terminado alcance histórico al entregar recorte |

Compatibilidad es de diseño, no resultado de migración. Snapshot inspeccionado: Backend 2cb124d, Database V1 b493fd1, V2 54b7644, contexto d481061; frontend sin HEAD. No se volvió a ejecutar ninguna prueba.
