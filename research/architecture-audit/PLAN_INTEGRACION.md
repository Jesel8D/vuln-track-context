# Plan maestro de integración

1. **Puerta de producto:** ambos desarrolladores aprueban alcance, tipos de activo, permisos y criterios de aceptación. Evidencia: decisiones registradas. Riesgo: trabajo divergente.
2. **Contrato:** Jesel propone API, ORM y manejo transaccional; Rudi propone SQL, restricciones y tests; revisión cruzada, según disponibilidad. El documento vigente distribuye A/B sin asignar nombres. Aceptación: contratos versionados y límites de responsabilidad D-002.
3. **Migración:** acordar baseline único, 0002, 004 y catálogo. Ensayar PostgreSQL desechable con datos de prueba, comprobaciones antes/después y restauración. No utilizar base persistente.
4. **Autorización:** primero propiedad por API y pruebas A/B; si se aprueban workspaces, implementar firma, provisión, roles y contexto local a transacción. Exigir pruebas de pool y RLS.
5. **Integración de dominio:** modelar componentes/CPE y resultados potenciales, mantener API existente o versionar cambios. NVD con dobles y límites; ZAP solo después de revisión de seguridad y autorización.
6. **CI y revisión:** suites unitarias, integración PostgreSQL aislada, migraciones, contratos, concurrencia y seguridad con identidades distintas; registrar versiones y resultados. Ambos revisan las contribuciones antes de acordar orden de integración.

Puerta final: cero fallos, evidencia de preservación de registros, ausencia de fuga entre usuarios/workspaces, rollback/restauración ensayados y aprobación humana. La ejecución actual no satisface esta puerta.

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.
