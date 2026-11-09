# Auditoría de seguridad

**Hecho:** `app/core/security.py` usa Argon2id y JWT HS256 con emisor, audiencia, expiración y versión de usuario; `api/dependencies.py` consulta usuario activo. `services/authorization.py` filtra propiedad; activos sin dueño o ajenos no se muestran. La limitación de login es por proceso; JWT no incorpora revocación individual ni refresh. CORS no es autorización.

**Hecho:** `004_v2_application_model.sql` habilita RLS en diez tablas nuevas, no en V1; usa `SECURITY DEFINER`, búsqueda restringida, HMAC ligado a usuario/workspace/expiración/nonce y `set_config(..., true)` local a transacción. La clave está en esquema privado; `vulntrack_app` no debería leerla. Políticas SELECT y UPDATE verifican rol; INSERT incluye controles específicos; DELETE solo concedido para membresías. Audit events concede INSERT y trigger impide UPDATE/DELETE.

**Riesgos no demostrados:** no hay `FORCE ROW LEVEL SECURITY`; dueños y BYPASSRLS pueden eludir políticas. La función de contexto verifica membresía existente antes de establecer identidad, lo que crea un problema de arranque para crear primer workspace/membresía desde rol app: diseñar provisión segura y probarla. El nonce está firmado pero no se observa registro de uso único: un contexto capturado puede reutilizarse hasta expirar. Una clave comprometida habilitaría firmas; su provisión y rotación no están implementadas en API. GUC manipulados con SET pueden persistir en pool; usar transacción explícita, LOCAL y pruebas de limpieza también en rollback/errores. Revisar permisos de schemas, owner de funciones, `public.hmac`, y vistas/errores. Las FK no sustituyen autorización.

**Pendiente:** probar roles efectivos sin superusuario ni propietario, todos los comandos, expiración, firma alterada, usuarios desactivados, revocación, reutilización y carreras. El test existente no alcanzó RLS en esta ejecución. URL válida no demuestra autorización para escanear ni previene SSRF, DNS rebinding o redirecciones; ZAP exige barreras independientes.

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.

## Matriz de riesgos y responsables sugeridos

Estimaciones cualitativas, no mediciones; probabilidad e impacto deben revisarse con el equipo. Todos son potenciales salvo el fallo de prueba indicado.

| Riesgo y evidencia | Probabilidad / impacto / severidad | Mitigación y prioridad | Responsable sugerido |
|---|---|---|---|
| R1 fuga entre usuarios: V1 sin RLS, acceso depende de filtros API (`authorization.py`, grants V1) | Media / alta / alta | pruebas negativas y mínimos privilegios; P0 | Jesel y Rudi |
| R2 acceso cruzado workspace: políticas no alcanzadas por suite actual | Desconocida / alta / alta | probar cada rol, tabla y operación en PostgreSQL aislado; P0 | Rudi |
| R3 escalamiento o bypass de owner/BYPASSRLS (`004` sin FORCE) | Baja si configuración correcta / alta / alta | login no propietario, verificar roles efectivos; P0 | Rudi |
| R4 exposición de clave HMAC o firma reutilizada | Desconocida / alta / alta | secretos, rotación, TTL, analizar replay; P0 | ambos |
| R5 contexto residual en pool | Desconocida / alta / alta | LOCAL transaccional, rollback y tests de reutilización; P0 | Jesel |
| R6 divergencia bootstrap/Alembic, pérdida o reasignación de datos | Media / alta / alta | prueba con datos ficticios y restauración; P0 | ambos |
| R7 compatibilidad rota en backend por catálogo V2 | Desconocida / alta / alta | ensayar baseline, 0002 y 004 con API; P0 | ambos |
| R8 CPE erróneo o CVE interpretado como confirmado | Media / alta / alta | selección humana, procedencia, evidencia y rótulo potencial; P1 | Jesel |
| R9 complejidad de workspaces excede plazo académico | Media / media / media | MVP acotado y puertas por capacidad; P1 | ambos |
| R10 fallo comprobado de suite: constraint trigger no encontrado por nombre sin esquema | Alta en ejecución observada / alta para verificación / alta | fijar resolución del nombre y repetir sin ocultar fallo; P0 | Rudi |
