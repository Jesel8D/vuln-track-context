# Matriz de trazabilidad

Las rutas siguientes son relativas a cada repositorio y se identifican por commit, no por números de línea. [H] hecho, [P] prueba, [R] riesgo, [A] acción.

| ID | Repo / rama / commit / archivo y símbolo | Evidencia y consecuencia | Acción |
|---|---|---|---|
| INT-001 | backend feat/backend-v1-assets `2cb124d` `app/core/security.py:decode_token`, `app/api/dependencies.py:current_user` | [H] JWT y usuario activo | [A] mantener tests de sesión y desactivación |
| INT-002 | backend `2cb124d` `app/services/authorization.py:visible_assets` | [H] filtro created_by; históricos sin dueño invisibles | [A] definir política de asignación |
| INT-003 | database origin/rudi `54b7644` `sql/004_v2_application_model.sql:applications` | [H] entidad web separada de assets | [A] decidir dominio |
| INT-004 | database `54b7644` mismo SQL: `workspace_members` | [H] owner/editor/viewer | [A] aprobar colaboración |
| INT-005 | database `54b7644` mismo SQL: `technologies`, `application_findings` | [H] prefijo CPE y CVE de aplicación | [A] validar semántica y duplicación |
| INT-006 | database `b493fd1` `sql/001_schema.sql:analysis_runs,findings` | [H] modelo V1 independiente | [A] decidir convergencia |
| INT-007 | database `54b7644` SQL: `recommendations,dependency_imports` | [H] almacenamiento adicional sin API | [A] priorizar posteriormente |
| INT-008 | backend `2cb124d` `app/services/assets.py:register` | [H] transacción y clave por usuario/ruta | [A] definir ámbito workspace |
| INT-009 | database `54b7644` SQL: `context_is_valid,establish_context` y políticas | [H] HMAC y RLS solo tablas nuevas; [R] replay y bypass de propietario | [A] pruebas por rol, pool y expiración |
| INT-010 | backend `2cb124d` `alembic/versions/0002_asset_owner.py`; database `54b7644` `init/00-bootstrap.sh` | [H] dos líneas de evolución | [A] contrato de catálogo y prueba conjunta |
| INT-011 | database `54b7644` `tests/002_v2_security.sql` | [P] abortó en constraint trigger con search_path por defecto | [A] reparar runner y repetir suite completa |
| INT-012 | backend `2cb124d` `app/api/router.py` | [H] rutas de activos/auth; sin workspace | [A] contrato y versión de API |
| INT-013 | context dev/jesel `d481061` `context/decisions.json` | [H] D-001 y D-002 aprobadas | [A] conservar responsabilidades |
| INT-014 | database `54b7644` `docs/TEST_RESULTS.md` | [H] V2 previamente declarada sin verificar | [A] conservar procedencia; no confundir instalación con validación |

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.
