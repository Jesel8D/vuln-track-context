# Contexto del proyecto

> **Actualización de estado — 2026-11-27:** la decisión [D-003](../context/decisions.json) establece aplicación web únicamente y descarta el cliente móvil por instrucción explícita del usuario solicitante. El alcance MVP del 2026-11-08 fue aprobado individualmente por Jesel; la ratificación de Rudi y los detalles técnicos globales siguen **PENDIENTES**. Véanse [registro de aprobación](MVP_APPROVAL_RECORD.md), [pendientes actuales](REMAINING_WORK_2026-11-27.md) y [contrato consolidado](VULNTRACK_MVP_CONTRACT.md). Las referencias móviles de documentos previos son antecedentes hasta su actualización; no describen el alcance vigente.

**ESTABLECIDA:** VulnTrack es un proyecto académico de gestión y seguimiento de vulnerabilidades informáticas. Busca registrar activos tecnológicos, consultar vulnerabilidades asociadas a productos y versiones mediante NVD y realizar análisis web controlados con OWASP ZAP Baseline.

**ESTABLECIDA:** VulnTrack se trabajará como aplicación web. No se desarrollará una aplicación móvil dentro del proyecto (D-003). El flujo vigente es navegador web → API → PostgreSQL. La distribución semanal y las responsabilidades individuales están **PENDIENTE** de aprobación.

**ESTABLECIDA:** la aplicación web administra activos y aplicaciones, permite la selección asistida de CPE, consulta CVE mediante el backend cuando NVD live se habilita y muestra el historial disponible. El backend centraliza la lógica, las consultas externas y la persistencia. La arquitectura implementada usa React/Vite/TypeScript, FastAPI y PostgreSQL; las decisiones de operación y los riesgos abiertos se detallan en [arquitectura](ARCHITECTURE.md) y [pendientes actuales](REMAINING_WORK_2026-11-27.md).

## Repositorios

- Backend: https://github.com/Jesel8D/vuln-track-backend
- Frontend: https://github.com/Jesel8D/vuln-track-frontend
- Base de datos: https://github.com/Jesel8D/vuln-track-database
- Contexto: https://github.com/Jesel8D/vuln-track-context

**APROBADA:** D-001 establece el repositorio central de contexto; D-002 distribuye responsabilidades de base de datos y backend. Consulta [decisiones](DECISIONS_AND_OPEN_QUESTIONS.md). El alcance y las exclusiones del MVP están en [requisitos](REQUIREMENTS.md).
