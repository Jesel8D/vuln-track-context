# Límites de seguridad

> **Actualización de estado — 2026-11-08:** Jesel aprobó individualmente el alcance del MVP de inventario/CPE/NVD sin ZAP ni workspaces operativos en primera entrega; ratificación de Rudi y detalles técnicos siguen PENDIENTES. Véanse [registro de aprobación](MVP_APPROVAL_RECORD.md) y [contrato consolidado](VULNTRACK_MVP_CONTRACT.md). El contenido anterior que sigue se conserva como antecedente; no constituye aprobación conjunta ni habilita ZAP. D-001 y D-002 permanecen intactas.

## Reglas establecidas

- **ESTABLECIDA:** NVD se consulta sin replicar su base de datos completa. Una persona selecciona el CPE entre candidatos; los CVE se presentan como potencialmente aplicables hasta su verificación, sin afirmar coincidencia automática exacta.
- **ESTABLECIDA:** solo se contempla OWASP ZAP Baseline, en análisis aislados y controlados de objetivos web autorizados y previamente incluidos en una lista permitida. No se permiten URLs arbitrarias ni Active Scan; tampoco análisis de routers, firewalls, redes completas o dispositivos físicos.
- **ESTABLECIDA:** Baseline no garantiza detectar SQL Injection ni otras vulnerabilidades específicas.
- **ESTABLECIDA:** las observaciones de activos son no sensibles. No almacenar contraseñas administrativas de routers ni secretos de clientes; no incluir credenciales, tokens ni claves en la documentación.

## Medidas y definiciones pendientes

- **PROPUESTA:** Docker para aislamiento; su adopción requiere aprobación.
- **PENDIENTE:** mecanismo de aislamiento, validación y administración de la lista permitida, verificación de autorización del objetivo y controles concretos de ejecución.
- **PENDIENTE:** autenticación, autorización, roles, gestión de credenciales operativas, protección de datos y controles de acceso. No se cuenta con diseños aprobados.
