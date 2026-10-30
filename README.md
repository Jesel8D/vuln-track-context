# VulnTrack: contexto central

Este repositorio reúne el contexto compartido del proyecto académico VulnTrack para desarrolladores y agentes de IA. La documentación central es independiente de los repositorios [backend](https://github.com/Jesel8D/vuln-track-backend), [frontend](https://github.com/Jesel8D/vuln-track-frontend) y [database](https://github.com/Jesel8D/vuln-track-database). Sus responsabilidades se describen en [arquitectura](docs/ARCHITECTURE.md).

## Consulta y mantenimiento

Antes de trabajar, lee [contexto](docs/PROJECT_CONTEXT.md), [requisitos](docs/REQUIREMENTS.md), [decisiones y preguntas](docs/DECISIONS_AND_OPEN_QUESTIONS.md), [límites de seguridad](docs/SECURITY_BOUNDARIES.md) y el documento específico de la tarea. Los índices legibles por máquina están en `context/`; `docs/` contiene la documentación y `diagrams/` los diagramas Mermaid. [AGENTS.md](AGENTS.md) establece instrucciones para agentes.

Los estados formales son `ESTABLECIDA` para alcance y restricciones conocidos, `APROBADA` para decisiones autorizadas, `PROPUESTA` para alternativas y `PENDIENTE` para definiciones faltantes. Solo D-001 y D-002 son decisiones formalmente aprobadas.

Al actualizar, identifica la fuente y el estado de cada cambio, mantén coherentes documentos, JSON y diagramas, y no presentes propuestas como aprobaciones. Registra una decisión con identificador, descripción, estado y justificación histórica verificable en `context/decisions.json` y `docs/DECISIONS_AND_OPEN_QUESTIONS.md`; una aprobación requiere autorización humana. Si hay contradicciones, señálalas y consulta a una persona responsable antes de resolverlas por suposición. No alteres decisiones aprobadas sin autorización humana.

Las contribuciones deben ser concretas, revisables y limitarse al alcance autorizado; no incluir secretos ni credenciales. Indica supuestos y pendientes, y revisa enlaces, JSON y coherencia antes de entregar.

## Índice de ingeniería

- [Auditoría documental](docs/DOCUMENTATION_AUDIT.md) y [trazabilidad de requisitos](docs/REQUIREMENTS_TRACEABILITY.md).
- [Criterios de aceptación](docs/ACCEPTANCE_CRITERIA.md) y [revisión de preparación](docs/IMPLEMENTATION_READINESS_REVIEW.md).
- [Investigación de integraciones](docs/EXTERNAL_INTEGRATIONS.md), [borrador de API](docs/API_CONTRACT_DRAFT.md) y [diccionario de datos propuesto](docs/DATA_DICTIONARY_DRAFT.md).
- [Modelo de amenazas](docs/THREAT_MODEL.md) y [evaluación de tecnologías](docs/STACK_EVALUATION.md).
- Diagramas propuestos: [registro de activos](diagrams/asset_registration_sequence.mmd), [evaluación CVE](diagrams/cve_assessment_sequence.mmd), [ZAP Baseline](diagrams/zap_baseline_sequence.mmd) y [límites de confianza](diagrams/trust_boundaries.mmd).

Los borradores y criterios concretos son **PROPUESTA**; las decisiones y definiciones faltantes son **PENDIENTE**. Consultar los documentos originales y decisiones antes de implementar.

## Auditoría y plan de implementación

- [Estado real de los repositorios](docs/REPOSITORY_READINESS.md) y [plan maestro](docs/IMPLEMENTATION_MASTER_PLAN.md).
- [Distribución propuesta entre desarrolladores](docs/DEVELOPER_WORK_DISTRIBUTION.md), [estrategia de pruebas](docs/TEST_STRATEGY.md) y [registro de riesgos](docs/TECHNICAL_RISK_REGISTER.md).
- [Diagrama de dependencias y puntos de integración](diagrams/implementation_roadmap.mmd).

La planificación es **PROPUESTA**; los requisitos establecidos y las decisiones **PENDIENTE** se conservan en los documentos de contexto.

## Paquete de decisiones y diagnóstico

- [Diagnóstico de repositorios](docs/REPOSITORY_ACCESS_DIAGNOSTICS.md) y [comparativa técnica](docs/TECHNICAL_DECISION_PACKAGE.md).
- [Ubicación móvil](docs/MOBILE_REPOSITORY_STRATEGY.md), [contrato de activos](docs/ASSET_CONTRACT_PROPOSAL.md) y [modelo propuesto](docs/DATA_MODEL_PROPOSAL.md).
- [Identidad y permisos](docs/IDENTITY_AND_ACCESS_PROPOSAL.md), [seguridad de integraciones](docs/INTEGRATION_SECURITY_REVIEW.md) y [matriz de decisiones](docs/DECISION_APPROVAL_CHECKLIST.md).
- [Flujo propuesto de activos](diagrams/proposed_asset_flow.mmd).

El [plan maestro](docs/IMPLEMENTATION_MASTER_PLAN.md) y el paquete de decisiones se integraron en `dev/jesel` el 30-10-2026; los commits originales se conservan en el historial. Todos los nuevos diseños siguen **PROPUESTA** y las decisiones correspondientes **PENDIENTE**.
