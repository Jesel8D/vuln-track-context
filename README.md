# VulnTrack: contexto central

Este repositorio reúne el contexto compartido del proyecto académico VulnTrack para desarrolladores y agentes de IA. La documentación central es independiente de los repositorios [backend](https://github.com/Jesel8D/vuln-track-backend), [frontend](https://github.com/Jesel8D/vuln-track-frontend) y [database](https://github.com/Jesel8D/vuln-track-database). Sus responsabilidades se describen en [arquitectura](docs/ARCHITECTURE.md).

## Consulta y mantenimiento

Antes de trabajar, lee [contexto](docs/PROJECT_CONTEXT.md), [requisitos](docs/REQUIREMENTS.md), [decisiones y preguntas](docs/DECISIONS_AND_OPEN_QUESTIONS.md), [límites de seguridad](docs/SECURITY_BOUNDARIES.md) y el documento específico de la tarea. Los índices legibles por máquina están en `context/`; `docs/` contiene la documentación y `diagrams/` los diagramas Mermaid. [AGENTS.md](AGENTS.md) establece instrucciones para agentes.

Los estados formales son `ESTABLECIDA` para alcance y restricciones conocidos, `APROBADA` para decisiones autorizadas, `PROPUESTA` para alternativas y `PENDIENTE` para definiciones faltantes. Solo D-001 y D-002 son decisiones formalmente aprobadas.

Al actualizar, identifica la fuente y el estado de cada cambio, mantén coherentes documentos, JSON y diagramas, y no presentes propuestas como aprobaciones. Registra una decisión con identificador, descripción, estado y justificación histórica verificable en `context/decisions.json` y `docs/DECISIONS_AND_OPEN_QUESTIONS.md`; una aprobación requiere autorización humana. Si hay contradicciones, señálalas y consulta a una persona responsable antes de resolverlas por suposición. No alteres decisiones aprobadas sin autorización humana.

Las contribuciones deben ser concretas, revisables y limitarse al alcance autorizado; no incluir secretos ni credenciales. Indica supuestos y pendientes, y revisa enlaces, JSON y coherencia antes de entregar.
