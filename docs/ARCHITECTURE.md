# Arquitectura

> **Actualización de estado — 2026-11-27:** D-003 aprueba producto web únicamente y descarta cliente móvil. La arquitectura que sigue combina estado implementado y antecedentes; no habilita ZAP ni despliegue público. Véanse [decisiones](DECISIONS_AND_OPEN_QUESTIONS.md) y [pendientes actuales](REMAINING_WORK_2026-11-27.md).

**ESTABLECIDA (alcance):** aplicación web cliente-servidor. Navegador web → API → PostgreSQL; no existe cliente móvil en el alcance actual (D-003). El diagrama [architecture.mmd](../diagrams/architecture.mmd) conserva un flujo conceptual histórico y no representa conexión directa del navegador a PostgreSQL.

## Componentes y tecnologías

- **APROBADA:** producto web únicamente; no se implementará cliente móvil (D-003).
- **ESTABLECIDA:** la aplicación web implementada usa React, Vite y TypeScript para consultar activos, aplicaciones, identificaciones CPE e historial; los resultados CVE dependen del modo de compilación y configuración del backend. Algunas superficies siguen siendo demostrativas.
- **ESTABLECIDA:** el backend implementado usa FastAPI y centraliza lógica, autorización, consultas externas y persistencia. SQLAlchemy y Alembic corresponden al backend (D-002).
- **ESTABLECIDA:** PostgreSQL es la base de datos implementada; el repositorio database contiene configuración, inicialización, seeds y documentación (D-002). La actualización de bases existentes requiere validación separada.
- **ESTABLECIDA:** NVD REST API 2.0 proporciona consultas CPE y CVE; no se replicará NVD completamente ni se presupone correspondencia automática exacta.
- **PENDIENTE:** ZAP Baseline no forma parte del MVP aprobado individualmente. Una fase futura requeriría nueva aprobación, autorización, controles de salida y aislamiento comprobados.

## Flujos

**ESTABLECIDA:** el flujo vigente es navegador web → API → PostgreSQL. Los clientes web consumen la API; no se conectan directamente a PostgreSQL.

**ESTABLECIDA:** el usuario selecciona un CPE entre candidatos antes de consultar CVE; los resultados son potencialmente aplicables hasta su verificación. El cliente CVE live existe en backend y la interfaz puede compilarse para live, pero las instalaciones nuevas lo mantienen deshabilitado por defecto. ZAP no se ejecuta en el alcance actual.
