# Auditoría ejecutiva — VulnTrack

**Conclusión: CONDICIONAL.** Puede comenzar la coordinación de diseño y el trabajo en entornos aislados; no debe integrarse ni desplegarse el conjunto de ramas todavía.

1. Backend V1.3 implementa login, JWT, activos por propietario e idempotencia; la propiedad es una propuesta técnica, no decisión formal aprobada.
2. Database V1 tiene cinco tablas y V2 agrega diez tablas para colaboración y aplicaciones. V2 preserva V1 pero no traslada la propiedad.
3. La prueba V2 se detiene antes de RLS: véase resultado reproducido abajo.
4. La API no firma ni establece contexto de workspace; V1 no queda protegida por RLS V2. La compatibilidad funcional y de catálogo no está demostrada.
5. V2 presupone aplicación web y workspaces, mientras la visión incluye activos tecnológicos: resolver alcance antes del modelo.

**Riesgos prioritarios:** acceso cruzado por permisos V1, fuga de contexto en conexiones reutilizadas, divergencia entre bootstrap/Alembic, asociación incorrecta CPE-CVE y datos históricos sin propietario. Son riesgos, no ataques demostrados.

**Recomendación:** mantener API y propiedad individual como propuesta de MVP, incorporar aplicaciones como posible subtipo con componentes/CPE confirmados y posponer colaboración/RLS hasta decisiones y pruebas; consultar alternativas en la propuesta. D-001 y D-002 siguen aprobadas. Decidir dominio, tenencia y ruta de migración antes de fusionar.

## Alcance y evidencia
Revisión estática de referencias locales, sin fetch: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Rudi `1a7ec7b`; database `b493fd17e9290ca94fa085d49230073ed1837e1a`, Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`; contexto `d481061`, Rudi `6c8baf7`. Las referencias remotas pueden no ser actuales. Frontend: rama main sin commits. Estados iniciales: backend y contexto limpios; database con modificación preexistente en `diagrams/database_v1.mmd` preservada. No se consultaron secretos.

**Prueba ejecutada:** PostgreSQL 16.15 Alpine local en contenedor desechable, red `none`, PGDATA tmpfs, sin volumen ni puerto; se ejecutaron secuencialmente V1, privilegios, V2 y seed con éxito en instalación limpia. `tests/002_v2_security.sql` abortó con `constraint "workspaces_require_owner" does not exist` tras dos rechazos esperados, antes de las pruebas RLS. Es una incidencia reproducida bajo search_path predeterminado, no demuestra que RLS falle ni que funcione. El segundo intento para diagnóstico falló por arranque del servidor; no aporta resultado. Los contenedores propios fueron eliminados. No se probaron migración con datos, Backend + V2 ni ataques completos. Los reportes de 77/122/29/55 son históricos y no se han reejecutado.
