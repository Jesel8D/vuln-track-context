# VulnTrack — contexto maestro para continuar

**Contrato del MVP aprobado por Jesel, pendiente de ratificación por Rudi.** Registro 2026-11-08; estado global PENDIENTE y detalles técnicos PROPUESTA.

VulnTrack registra inventario manual de routers, switches y servidores, confirma un CPE principal mediante intervención humana y consulta NVD para resultados potencialmente aplicables con historial/cobertura. No escáneres de dispositivos, workspaces operativos ni ZAP operativo en primera entrega según aprobación individual. Una coincidencia CVE/CPE no demuestra explotación ni aplicabilidad real sin condiciones/evidencia.

Fuentes normativas: [D-001/D-002](../context/decisions.json) permanecen intactas. [Registro individual](MVP_APPROVAL_RECORD.md) delimita qué aceptó Jesel; no inventar aprobación de Rudi. [Contrato maestro](VULNTRACK_MVP_CONTRACT.md) define formulario, identificación, CVE, datos/API y 38 AC, todos con detalle técnico pendiente de revisión.

Implementación presente: Backend V1.3 `2cb124d`, Database V1 `b493fd1`; propuesta V2 `54b7644` y auditoría `2c97231` separadas. Frontend local y remoto sin ramas al revisar; no app React demostrada. Backend todavía no implementa CPE/CVE ni version=null; ownership existe en ORM/migración propuesta, no baseline SQL.

Leer [handoff](PROJECT_HANDOFF.md), [guía nueva PC](SETUP_NEW_PC.md), [inventario](REMOTE_BACKUP_INVENTORY.md) y [mapa de fuentes](../research/SOURCE_IMPORT_MANIFEST.md). La aprobación de alcance no aplica SQL, no integra V2 y no habilita producción. La distribución de trabajo es flexible; modelos/migraciones Alembic se coordinan con bootstrap y SQL según D-002.
