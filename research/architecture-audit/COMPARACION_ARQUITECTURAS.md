# Comparación de arquitecturas

Backend Rudi (`1a7ec7b`) contiene README y gitignore, no una segunda API. Database Rudi parte directamente de `b493fd1`, añade `66b68aa` y `54b7644`. Contexto Rudi parte de `d481061`. Backend Jesel y Rudi comparten `b5e71f2`; no tratar el diff inverso como contribuciones eliminadas.

| ID / componente | Jesel | Rudi | Compatibilidad, impacto y prioridad |
|---|---|---|---|
| INT-001 usuarios/autenticación | users, Argon2id y JWT | reutiliza users; sin API | Parcial; definir identidad activa y secreto. Alta |
| INT-002 autorización | created_by, filtro en servicio | membresías, RLS y HMAC | Modelos distintos; no sustituir sin diseño. Crítica |
| INT-003 activos/aplicaciones | assets inventario | applications separado, targets | Complementarios sin relación automática; decidir dominio. Alta |
| INT-004 workspaces | inexistentes | owner/editor/viewer | Cambio de producto y contrato. Alta |
| INT-005 CPE/CVE | tablas V1 y prefijo CPE | technologies y application_findings | Duplicación conceptual; validación semántica pendiente. Alta |
| INT-006 análisis/hallazgos | analysis_runs/findings | tablas de aplicación separadas | Sin adaptación API/ORM. Alta |
| INT-007 recomendaciones/importaciones | ausentes | tablas nuevas | Aditivo en SQL, funcionalidad no implementada. Media |
| INT-008 idempotencia | clave por usuario/método/ruta, snapshot | conserva V1 sin ámbito workspace | Rediseñar antes de solicitudes colaborativas. Alta |
| INT-009 RLS | filtros API | RLS solo tablas nuevas | No protege V1; bootstrap de identidad pendiente. Crítica |
| INT-010 Alembic | baseline verificado y 0002 | 004 SQL en init | catálogo y secuencia deben reconciliarse. Crítica |
| INT-011 Docker | imagen API | Compose inicializa 004 | instalación distinta de V1; probar conjuntamente. Alta |
| INT-012 HTTP/clientes | siete rutas reales; CORS explícito | no implementa rutas | no hay contrato de workspaces; frontend vacío. Alta |
| INT-013 seguridad | límites y roles restringidos | SECURITY DEFINER y firma | clave, sesión y permisos requieren integración. Crítica |

Conservar backend V1.3 es viable como línea de partida, no como compatibilidad certificada con V2. Rudi aporta estructuras y restricciones reutilizables; V2 no convierte dispositivos en aplicaciones y no implementa NVD ni ZAP.

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.
