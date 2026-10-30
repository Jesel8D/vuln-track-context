# Contexto del proyecto

**ESTABLECIDA:** VulnTrack es un proyecto académico de gestión y seguimiento de vulnerabilidades informáticas. Busca registrar activos tecnológicos, consultar vulnerabilidades asociadas a productos y versiones mediante NVD y realizar análisis web controlados con OWASP ZAP Baseline.

**ESTABLECIDA:** dos desarrolladores disponen de aproximadamente cuatro semanas. La prioridad es el flujo aplicación móvil → API → PostgreSQL → aplicación web. La distribución semanal y las responsabilidades individuales están **PENDIENTE** de aprobación.

**ESTABLECIDA:** la aplicación móvil registra activos desde campo y los envía al backend; la web administra y consulta activos, permite la selección asistida de CPE, consulta CVE y visualiza hallazgos y análisis; el backend centraliza lógica, consultas externas y persistencia. PostgreSQL es la base de datos contemplada en el alcance; su adopción técnica está clasificada como **PROPUESTA**. La arquitectura y el detalle de administración están en [arquitectura](ARCHITECTURE.md) y [requisitos](REQUIREMENTS.md).

## Repositorios

- Backend: https://github.com/Jesel8D/vuln-track-backend
- Frontend: https://github.com/Jesel8D/vuln-track-frontend
- Base de datos: https://github.com/Jesel8D/vuln-track-database
- Contexto: https://github.com/Jesel8D/vuln-track-context

**APROBADA:** D-001 establece el repositorio central de contexto; D-002 distribuye responsabilidades de base de datos y backend. Consulta [decisiones](DECISIONS_AND_OPEN_QUESTIONS.md). El alcance y las exclusiones del MVP están en [requisitos](REQUIREMENTS.md).
