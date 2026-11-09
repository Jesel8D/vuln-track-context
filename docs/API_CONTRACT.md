# Contrato API

> **Actualización de estado — 2026-11-08:** Jesel aprobó individualmente el alcance del MVP de inventario/CPE/NVD sin ZAP ni workspaces operativos en primera entrega; ratificación de Rudi y detalles técnicos siguen PENDIENTES. Véanse [registro de aprobación](MVP_APPROVAL_RECORD.md) y [contrato consolidado](VULNTRACK_MVP_CONTRACT.md). El contenido anterior que sigue se conserva como antecedente; no constituye aprobación conjunta ni habilita ZAP. D-001 y D-002 permanecen intactas.

Todos los siguientes endpoints y sus propósitos son **PROPUESTA**; no constituyen contratos definitivos ni autorizan operaciones cuyo alcance sigue pendiente.

| Método y ruta | Propósito | Estado |
|---|---|---|
| `POST /auth/login` | Iniciar sesión | PROPUESTA |
| `GET /auth/me` | Consultar la identidad actual | PROPUESTA |
| `GET /assets` | Consultar activos | PROPUESTA |
| `POST /assets` | Registrar un activo | PROPUESTA |
| `GET /assets/{id}` | Consultar un activo | PROPUESTA |
| `PATCH /assets/{id}` | Actualizar un activo; alcance de actualización pendiente | PROPUESTA |
| `DELETE /assets/{id}` | Eliminar un activo; alcance de eliminación pendiente | PROPUESTA |
| `GET /cpe/search?q=` | Buscar candidatos CPE | PROPUESTA |
| `POST /assets/{id}/cve-check` | Solicitar consulta de CVE para un activo y CPE seleccionado | PROPUESTA |
| `POST /scans` | Solicitar un análisis autorizado | PROPUESTA |
| `GET /scans/{id}` | Consultar un análisis | PROPUESTA |
| `GET /findings` | Consultar hallazgos | PROPUESTA |
| `PATCH /findings/{id}/status` | Proponer actualización de estado de un hallazgo | PROPUESTA |
| `GET /dashboard/summary` | Consultar resumen del dashboard | PROPUESTA |

**PENDIENTE:** esquemas completos de solicitud y respuesta, autenticación definitiva, autorización y roles, validaciones, formato de errores, paginación, códigos HTTP y reglas de negocio específicas por endpoint. Los mecanismos para transmitir el CPE seleccionado y controlar objetivos autorizados también están pendientes. Ver [requisitos](REQUIREMENTS.md) y [seguridad](SECURITY_BOUNDARIES.md).
