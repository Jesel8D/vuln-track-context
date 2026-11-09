# Decisiones humanas pendientes

Todas las recomendaciones siguen pendientes. D-001 (repositorio de contexto) y D-002 (Database SQL, Backend ORM/Alembic) permanecen aprobadas.

| ID | Pregunta y alternativas | Recomendación, impacto y fase límite |
|---|---|---|
| DEC-01 | dispositivos, web o ambos | ambos como visión; inventario primero; define producto antes del esquema |
| DEC-02 | propiedad individual o workspace | individual para MVP; colaboración requiere roles y UX; antes de autorización |
| DEC-03 | RLS ahora o posterior | posterior salvo colaboración obligatoria; capa adicional, mayor operación; antes de despliegue |
| DEC-04 | relación activo/CPE | componentes múltiples y confirmación; altera UI/modelo; antes de NVD |
| DEC-05 | persistencia CVE | fuente, tiempo, cobertura, deduplicación y revisión; antes de ingesta |
| DEC-06 | NVD alcance | búsqueda y resultados potenciales con manejo de fallos; antes de implementación |
| DEC-07 | ZAP alcance | posponer hasta controles y autorización demostrados; antes de cualquier ejecución |
| DEC-08 | propiedad histórica | no asignar por inferencia, resolver registros manualmente; antes de migrar |
| DEC-09 | SQL/Alembic coordinación | contrato de catálogo y migración revisada por ambos; antes de integración |
| DEC-10 | claves firmadas y bootstrap workspace | si se adopta V2, definir provisión, rotación y primer owner; antes de habilitar roles |

Cada opción comporta costes: solo dispositivos recorta alcance web; solo web excluye inventario; múltiples tipos elevan complejidad. Workspaces habilitan colaboración pero amplían superficie de aislamiento y pruebas.

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.
