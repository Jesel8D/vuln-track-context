# Propuesta de arquitectura objetivo

**Propuesta, no aprobación.** Opción 1: MVP de propiedad individual, inventario existente, componentes con uno o más CPE confirmados por humano, análisis NVD con fuente/cobertura y CVE potenciales; coste relativo bajo-medio, depende de aprobar propiedad y contrato CPE. Opción 2: agregar aplicaciones web como tipo de activo y targets autorizados, conservando dispositivos; coste medio-alto por modelo, formularios y migración. Opción 3: workspaces, membresías, RLS y auditoría con segregación integral; coste alto, depende de identidad firmada, provisión y pruebas adversariales. Recomiendo opción 1, diseñando extensión hacia opción 2; colaboración se difiere hasta requerimiento explícito.

| Dominio | Ventaja | Desventaja y viabilidad |
|---|---|---|
| Solo dispositivos | preserva V1, menor esfuerzo | excluye aplicación web y ZAP futuro |
| Solo aplicaciones web | aprovecha modelo Rudi | abandona inventario original y separa históricos |
| Varios tipos | permite equipos y aplicaciones | modelo, API y UI más complejos; implementar progresivamente |

Un activo no se convierte en CPE: representa producto/versiones mediante componentes; guardar fuente, selección humana y más de un CPE cuando aplique. Persistir CVE con fuente, fecha, cobertura y deduplicación, distinguir revisión humana de coincidencia potencial; nunca inferir explotación. NVD requiere paginación, límites y fallos explícitos. ZAP solo para destinos autorizados con aislamiento de red, DNS y redirecciones: etapa posterior. Clientes web/móvil acceden por API; Database mantiene SQL y Backend ORM/Alembic conforme D-002. RLS puede ser defensa adicional futura, no reemplazo automático de autorización en API.

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.
