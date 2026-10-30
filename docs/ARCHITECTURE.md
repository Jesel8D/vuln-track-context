# Arquitectura

**PROPUESTA:** arquitectura modular cliente-servidor, con aplicación móvil y web que utilizan un backend para lógica de negocio, integraciones y persistencia. El diagrama [architecture.mmd](../diagrams/architecture.mmd) muestra el flujo conceptual, no un contrato de conexiones definitivo.

## Componentes y tecnologías

- **ESTABLECIDA:** la aplicación móvil registra activos y conserva lo introducido ante fallos de envío. **PROPUESTA:** React Native, Expo y TypeScript.
- **ESTABLECIDA:** la web consulta y administra activos, presenta candidatos CPE, CVE potenciales, dashboard y resultados de análisis. Actualización detallada y eliminación están **PENDIENTE** de definición. **PROPUESTA:** React, Vite y TypeScript.
- **ESTABLECIDA:** el backend centraliza lógica, consultas externas y persistencia. **PROPUESTA:** Python y FastAPI; SQLAlchemy y Alembic para modelos y migraciones. **APROBADA:** D-002 sitúa dichos modelos y migraciones en backend si se adoptan.
- **ESTABLECIDA:** la base de datos contemplada es PostgreSQL; su adopción técnica es **PROPUESTA**. **APROBADA:** D-002 atribuye a database la configuración de PostgreSQL, inicialización, seed y documentación propia.
- **ESTABLECIDA:** NVD REST API 2.0 proporciona consultas CPE y CVE; no se replicará NVD completamente ni se presupone correspondencia automática exacta.
- **ESTABLECIDA:** OWASP ZAP Baseline analiza solo objetivos web autorizados en lista permitida, bajo los límites de [seguridad](SECURITY_BOUNDARIES.md). **PROPUESTA:** Docker para aislar la ejecución.

## Flujos

**ESTABLECIDA:** el flujo prioritario es móvil → API → PostgreSQL → web: el backend recibe y persiste registros y la web los consulta mediante el backend. La flecha PostgreSQL → web en el diagrama representa disponibilidad conceptual de los datos, no acceso directo desde el navegador.

**ESTABLECIDA:** el usuario selecciona un CPE entre candidatos antes de consultar CVE; los resultados son potencialmente aplicables hasta su verificación. La web solicita y consulta análisis Baseline mediante el backend. Detalles de comunicación, autenticación y ejecución están **PENDIENTE**.
