# Auditoría real de repositorios

Instantánea de inspección: 30-10-2026, antes de crear la rama de este trabajo. **Hecho verificado:** se consultaron los cuatro repositorios locales, sus remotos, estado, ramas, archivos rastreados y archivos de trabajo; `git ls-remote --symref origin HEAD 'refs/heads/*'` terminó correctamente para cada repositorio de implementación y no devolvió HEAD ni ramas. Eso solo describe referencias visibles en el momento de la consulta; no demuestra que no existan objetos o referencias no anunciadas en el servidor. No se alteraron esos repositorios.

**Información documentada:** alcance y estados están en [contexto](PROJECT_CONTEXT.md), [requisitos](REQUIREMENTS.md) y [decisiones](DECISIONS_AND_OPEN_QUESTIONS.md). **Inferencia:** no existe evidencia accesible de implementación en la copia o las ramas anunciadas de los tres repositorios de aplicación. No se puede afirmar cobertura 0 %: no hay pruebas ni herramienta de cobertura que medir.

| Repositorio | Remoto verificado | Rama y estado inicial | Contenido y tecnología detectados | Riesgos y brechas |
|---|---|---|---|---|
| backend | `https://github.com/Jesel8D/vuln-track-backend.git` | `main` sin commit; limpio, sin conflictos; sin ramas remotas anunciadas. | Solo `.git`; sin archivos rastreados, ignorados o de trabajo. Ningún lenguaje, framework, dependencia, configuración, código, pruebas ni documentación detectables. | Falta API, identidad, persistencia, integración NVD/ZAP y pruebas; no hay código sobre el cual medir avance. |
| frontend | `https://github.com/Jesel8D/vuln-track-frontend.git` | `main` sin commit; limpio, sin conflictos; sin ramas remotas anunciadas. | Solo `.git`; sin archivos rastreados, ignorados o de trabajo. No se detectaron lenguaje, framework, dependencias, configuración, código, pruebas ni documentación. | No existe interfaz de consulta, selección CPE, resultados ni dashboard en la copia revisada. |
| database | `https://github.com/Jesel8D/vuln-track-database.git` | `main` sin commit; limpio, sin conflictos; sin ramas remotas anunciadas. | Solo `.git`; sin archivos rastreados, ignorados o de trabajo. No se detectaron configuración PostgreSQL, scripts, seed, pruebas ni documentación. | Entorno de persistencia no preparado; D-002 asigna configuración, inicialización, seed y documentación a este repositorio. |
| context | `git@github.com:Jesel8D/vuln-track-context.git` | `dev/jesel` en `ec8f432`, limpio y sincronizado tras fetch; este trabajo parte de ese commit. | Archivos `AGENTS.md`, `README.md`, `context/` (dos JSON), `docs/` y `diagrams/`; documentación Markdown/JSON/Mermaid. No contiene aplicación ni pruebas de aplicación. | Contiene borradores, no código ejecutable; decisiones de producto, seguridad y tecnología pendientes. |

No existe un repositorio móvil identificado en la estructura inspeccionada ni una ubicación aprobada en la documentación. **PENDIENTE:** decidir si vivirá en una carpeta del frontend, en un repositorio independiente u otra ubicación autorizada; no crearla por suposición. Los nombres `backend`, `frontend`, `database` son carpetas locales y corresponden a los remotos anteriores; el directorio central se llama `context-general-ia`.

## Contraste alcance frente a evidencia

| Alcance documentado | Evidencia real | Consecuencia / primer trabajo propuesto |
|---|---|---|
| Registro móvil, conservar formulario y reintentar (RF-02/03) | No se detectó aplicación móvil ni repositorio asignado. | Decidir ubicación, plataforma y retención; preparar cliente y prueba de pérdida de respuesta. |
| Recepción, persistencia y consulta web (RF-04/05/06) | Backend, database y frontend vacíos. | Aprobar contrato/identidad/esquema y construir primer flujo vertical. |
| Administración de activos (RF-07/08) | Sin implementación. | Registro/consulta son alcance; actualización y eliminación siguen **PENDIENTE**. |
| CPE y NVD, resultados potenciales (RF-09/10/11/12) | Sin cliente, interfaz ni hallazgos implementados. | Selección humana y fallos explícitos; verificar la documentación NVD que el estudio previo no pudo consultar directamente. |
| ZAP Baseline autorizado (RF-14, RN-05) | Sin ejecutor, lista, aislamiento o controles. | No habilitar análisis hasta demostrar autorización y control de salida por conexión. |
| Dashboard (RF-15) | Sin interfaz ni métricas implementadas. | Acordar métricas, visibilidad y fecha de actualización. |
| Identidad, roles, modelo y API | Borradores en contexto; no hay aplicación que los haga efectivos. | Aprobar lo mínimo para primera vertical y validar por pruebas. |

No se encontraron implementaciones sin requisito, duplicados de código ni contradicciones verificables entre código y documentación porque no hay código accesible; esto no prueba que estén ausentes en ubicaciones no inspeccionadas. El diagrama arquitectónico original representa PostgreSQL → web como flujo conceptual y su texto aclara acceso mediante backend. PostgreSQL está en el flujo documentado mientras su adopción técnica sigue **PROPUESTA**. D-001 y D-002 permanecen **APROBADA**, sin justificación histórica registrada.

**PENDIENTE:** stack y versiones, ubicación móvil, plataforma y distribución, contrato mínimo, esquema y validaciones, identidad/permisos, idempotencia, entorno de prueba y responsables de destinos ZAP. Las carpetas y módulos del [plan maestro](IMPLEMENTATION_MASTER_PLAN.md) son ejemplos propuestos, no estructuras detectadas.
