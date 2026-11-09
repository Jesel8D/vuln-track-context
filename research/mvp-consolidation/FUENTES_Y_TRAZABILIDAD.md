# Fuentes, contradicciones y trazabilidad

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Inventario y alcance de inspección

Fecha de consolidación 2026-11-06 (America/Mexico_City). Lectura local, sin fetch ni consultas nuevas a NVD. Los resultados externos son evidencia histórica de investigación, no revalidación. Se leyó contexto central y documentación de auditoría/investigación; se contrastaron esquema V1, propuesta V2 y rutas/schemas/servicios/migración de backend. PROJECT_MASTER_CONTEXT.md no se encontró en el snapshot inspeccionado. No se inspeccionaron secretos ni bases persistentes.

| Fuente | Rama/commit inspeccionado | Símbolos/rutas y uso |
|---|---|---|
| Backend | feat/backend-v1-assets, 2cb124d7cc41e22a62a305760826240f9208c257 | app/api/router.py, app/schemas/assets.py:AssetCreate, app/services/assets.py:register/request_fingerprint, app/services/authorization.py:visible_assets, app/core/cors.py, app/models/database_v1.py, alembic/versions/0002_asset_owner.py; contratos, riesgos y compatibilidad |
| Database V1 | feat/postgresql-database-v1, b493fd17e9290ca94fa085d49230073ed1837e1a | sql/001_schema.sql, sql/002_privileges.sql: cinco tablas, restricciones/índices/grants |
| Database V2 | origin/rudi, 54b7644467a1096c011daf3e5c23d7f9a7773f93 | sql/004_v2_application_model.sql: entidades, tecnologías, RLS/HMAC; alternativas no aprobadas |
| Contexto | dev/jesel, d481061e5ecb53b9a033086481add37f04d8c549 | README, AGENTS, context/project.json, context/decisions.json y docs obligatorios de misión: fuente de decisiones/alcance |
| Contexto Rudi | origin/rudi, 6c8baf74a3d7bcd7a0a9ebcca2dda0d797cbfeeb | docs/DECISIONS_AND_OPEN_QUESTIONS.md y DATABASE_V1_PROGRESS.md: dominio/workspaces aún pendientes |
| Validación posterior V2 | audit/database-v2-security-validation, 2c9723146e910aa51211958556961b4da7019ce3 | /home/mz/Desktop/vulntrack-database-v2-audit/docs/VALIDACION_SEGURIDAD_V2.md: resultado incompleto, no garantía de integración |
| Frontend | main sin commits/HEAD | sin aplicación local funcional demostrada; no se infiere estado remoto |

Rutas de repositorios bajo /home/mz/Desktop/VulnTrack. El cambio local diagrams/database_v1.mmd de Database se conserva; hash inicial 1bd797be050bca20fe07b1d8f158ada384703e11fc17f8eaf8ed16ebdbaba590. No ramas cambiadas, commits, migraciones ni pruebas operativas de esta especificación.

## Evidencia histórica de NVD

La investigación documenta 12 búsquedas de productos/familias con candidatos, todas múltiples; se leyeron primeras páginas de hasta cinco, no catálogo completo. RV340 firmware 1.0.0.33 → CVE-2017-6784, 1/1; TL-SG108E firmware 1.1.2 → CVE-2017-8074 y CVE-2017-8075, 2/5; Ubuntu Linux 10.04 LTS → CVE-2008-5983 y CVE-2009-2797, 2/521. Datos históricos de la consulta UTC del 2026-11-06 en PRUEBAS_CONCEPTO.md/FUENTES_Y_EVIDENCIAS.md; ninguna vulnerabilidad de equipo real fue confirmada. No se comprobó aquí recencia ni cobertura universal. Versiones históricas no son recomendación de despliegue.

Referencias oficiales conservadas: [NVD Products](https://nvd.nist.gov/developers/products), [NVD CVE API](https://nvd.nist.gov/developers/vulnerabilities), [NVD Start Here](https://nvd.nist.gov/developers/start-here), [CPE FAQ](https://nvd.nist.gov/general/faq-sections/cpe-faqs), [configuraciones](https://nvd.nist.gov/vuln/Vulnerability-Detail-Pages), [NIST IR 7695](https://csrc.nist.gov/pubs/ir/7695/final). No se atribuye a Start Here una cuota leída y confirmada en esta sesión. `virtualMatchString`, cuotas vigentes y condiciones de versión del filtro deben verificarse antes de implementación operativa. Las cifras de presupuesto de este contrato son propuestas internas, no límites oficiales.

## Contradicciones y cambios que requieren acuerdo

| ID | Fuentes en conflicto | Impacto | Resolución propuesta, no automática |
|---|---|---|---|
| CT-01 | Contexto REQUIREMENTS/IMPLEMENTATION_MASTER_PLAN incluye ZAP Baseline; investigación recomienda diferirlo y esta misión excluye análisis activo | Una demo NVD no cumple todo el alcance histórico | DEC-01 debe aprobar una fase reducida; conservar requisito ZAP pendiente y restricciones. Esta misión no habilita ningún escáner |
| CT-02 | Contexto prioriza móvil→API→web; investigación plantea priorizar un cliente si no hay capacidad | Riesgo de quitar móvil silenciosamente | DEC-12 mantiene móvil mínimo o exige recorte explícito/plazo, no tratar web como sustituto aprobado |
| CT-03 | V1 exige version string NOT NULL; investigación pide desconocida | JSON null falla hoy | DEC-06: migración aditiva compatible con datos existentes y adaptación de clientes |
| CT-04 | V2 se centra en aplicaciones/workspaces; V1 inventario | Dos dominios y autorizaciones sin integración | DEC-02/09: mantener inventario para fase1 y posponer 004 |
| CT-05 | Contexto dice datos del modelo «pendientes» y reportes Database antiguos dicen backend ausente; código 2cb124d sí implementa API/propiedad | Documentación histórica no describe implementación actual | Citar código con commit, no convertir implementación en aprobación |
| CT-06 | Propuestas usan cpe-selection/selection_id y rutas /cve-check; contexto /cpe/search | Contratos divergentes de operaciones no implementadas | Adoptar rutas de CONTRATO_API_MVP.md y campo identification_id solo si DEC-10 se aprueba |
| CT-07 | Investigación usa «sin coincidencia» como posible estado y luego recomienda tres estados | Duplicación de estados de búsqueda e identificación | Estado pending virtual, confirmed/needs_review en eventos; sin coincidencia resultado efímero |
| CT-08 | Auditoría antecedente no alcanzó RLS; rama audit posterior informa tests corregidos y ejecución parcial de seguridad | Podría leerse «V2 validada» erróneamente | 2c97231 es evidencia posterior parcial, mantiene validación incompleta; no reejecutada aquí |

## Trazabilidad de decisiones y requisitos

| Decisiones | Requisitos y criterios | Entregables que concretan |
|---|---|---|
| DEC-01/02/12 | RF-01/07/10, RNF-06, AC-F08 | maestro, formulario, plan |
| DEC-03 | RF-02, RNF-01, AC-A02/F01/F02 | API, modelo y compatibilidad |
| DEC-04/05 | RF-03/04, AC-B01–C06 | identificación, modelo, API |
| DEC-06 | RF-01/05, AC-A04/D01 | formulario y consulta CVE |
| DEC-07/08 | RF-06/08, AC-D03–E06 | CVE, modelo y API |
| DEC-09/10 | RNF-07, AC-F07 | compatibilidad/plan, sin alterar D-002 |
| DEC-11 | RNF-03, AC-D03/D06/F06 | consulta CVE y API |

## Documentos leídos: investigacion-nvd-cpe

- [CONTRATO_API_PROPUESTO.md](../nvd-cpe/CONTRATO_API_PROPUESTO.md) — SHA-256 `29a59244e4c09816ce332af32e66470b6d84e9a8e86cb732c097c4ca2acf7a9c`.
- [DECISIONES_PENDIENTES.md](../nvd-cpe/DECISIONES_PENDIENTES.md) — SHA-256 `cb36d64ef70e39d4d83895edbfa5657d21e8e91b3dbb1a895a309ddc40ecf181`.
- [FORMULARIO_REGISTRO_ACTIVOS.md](../nvd-cpe/FORMULARIO_REGISTRO_ACTIVOS.md) — SHA-256 `6222140a4d7d5528749fa355fe16e6e714bee7e2f37eaa4dcb609d5b36a12e62`.
- [FUENTES_Y_EVIDENCIAS.md](../nvd-cpe/FUENTES_Y_EVIDENCIAS.md) — SHA-256 `6437d42469089858063a4e6dca1a7f9ab24ddbe3adcb60f027c16cdd6d113f59`.
- [FUNDAMENTOS_CPE_NVD.md](../nvd-cpe/FUNDAMENTOS_CPE_NVD.md) — SHA-256 `96e20f0203abb36e2e1f7404177bb0915cd7c9c247e1a00bca6b4004f64af823`.
- [MATRIZ_COMPATIBILIDAD.md](../nvd-cpe/MATRIZ_COMPATIBILIDAD.md) — SHA-256 `81c018afaaeece532418a9ec0d1802c3399f25ea81f4a0a6e9a9a083df36b82f`.
- [MATRIZ_PRODUCTOS_NVD.md](../nvd-cpe/MATRIZ_PRODUCTOS_NVD.md) — SHA-256 `d5e75933c68309f077f1b57bb21514f0842bef9ca9ce5f39c3a4257d16318c65`.
- [MODELO_DATOS_CPE_CVE.md](../nvd-cpe/MODELO_DATOS_CPE_CVE.md) — SHA-256 `7408b0cc43d48fdc7c77b8f9df736f8527927024cbfd46155e29ff4a553bb9a9`.
- [PLAN_IMPLEMENTACION.md](../nvd-cpe/PLAN_IMPLEMENTACION.md) — SHA-256 `982d7ef565389179d11776e89a0ef5d29b2ff1db2f41cca409f4ca0794db0e1d`.
- [PRUEBAS_CONCEPTO.md](../nvd-cpe/PRUEBAS_CONCEPTO.md) — SHA-256 `38f343356d65ab22adb353aa9e8e6990d1a801da94ff314362388ead19572ecd`.
- [RESUMEN_EJECUTIVO.md](../nvd-cpe/RESUMEN_EJECUTIVO.md) — SHA-256 `9c122957f9c057065119b9ee279533e497164c78854dceee9ee75ef273059df5`.

## Documentos leídos: auditoria-integracion

- [ANALISIS_MIGRACIONES.md](../architecture-audit/ANALISIS_MIGRACIONES.md) — SHA-256 `e40ff4d0d8ccc83e0ec993ce69c9003467f6c21b1921ccc8df3d4446dbdae46b`.
- [AUDITORIA_EJECUTIVA.md](../architecture-audit/AUDITORIA_EJECUTIVA.md) — SHA-256 `d4a12a4009e944d2f1ac0d6ead3555212b7c8f02b26f46551309d926909e0c82`.
- [AUDITORIA_SEGURIDAD.md](../architecture-audit/AUDITORIA_SEGURIDAD.md) — SHA-256 `9851ac69f76eafe1f034193886f8c18b675112c29fb86172c705523c2a60d2d6`.
- [COMPARACION_ARQUITECTURAS.md](../architecture-audit/COMPARACION_ARQUITECTURAS.md) — SHA-256 `553d9c739b0e36666233575e0497982c5b8aa6eacd0be2284784b66fc6849e58`.
- [DECISIONES_PENDIENTES.md](../architecture-audit/DECISIONES_PENDIENTES.md) — SHA-256 `437dc1bb481aaa46dbecd90c7f0fb9caf48780f06f2916b3e2fd48a2c2355aaf`.
- [MATRIZ_TRAZABILIDAD.md](../architecture-audit/MATRIZ_TRAZABILIDAD.md) — SHA-256 `06a4b5a8ae7535bf05784b1a8834e6f18c7f68cd10ec043f88b9d8d7fe956748`.
- [PLAN_INTEGRACION.md](../architecture-audit/PLAN_INTEGRACION.md) — SHA-256 `b51cd37db2faab2d67f1973794744b21ba493cdee9cd33d96b08794348c32029`.
- [PROPUESTA_ARQUITECTURA_OBJETIVO.md](../architecture-audit/PROPUESTA_ARQUITECTURA_OBJETIVO.md) — SHA-256 `12cee699abc4f4e68269d200e5b38ccbb5e6412c9ac8972a4cf3f3ae4152a90f`.

## Revisión cruzada de los doce entregables

Revisión documental de esta versión; no es aprobación del diseño ni pruebas operativas. Se compararon nombres canónicos, rutas, estados, pertenencia de recursos, transacciones, campos y criterios; se analizaron consistencia y enlaces locales. Se corrigió una ambigüedad de recuperación del worker: una ejecución interrumpida con páginas válidas persistidas termina partial; sin páginas válidas termina failed, coherente con la política de cobertura. Se explicitó evidence nullable solo para legado sin procedencia recuperable, obligatorio en ingestas nuevas.

| Documento | Contrastado con | Resultado de revisión |
|---|---|---|
| VULNTRACK_MVP_CONTRACT_PROPOSAL.md | otros once | 30 secciones; mismos campos, estados y ocho operaciones nuevas; propuesta no aprobada |
| FORMULARIO_ACTIVOS_DEFINITIVO_PROPUESTO.md | modelo/API/AC-A04 | version nullable requiere adaptación; no inventa campo display_name persistido |
| CONTRATO_IDENTIFICACION_CPE.md | modelo/API/AC-C01–C06 | estado pending virtual, eventos confirmed/needs_review; un actual; history sin tabla redundante |
| CONTRATO_CONSULTA_CVE.md | modelo/API/AC-D01–D07/F10 | coverage independiente; worker recuperación parcial/fallida coherente; sin confirmed en findings |
| MODELO_DATOS_PROPUESTO.md | formulario/CPE/CVE/API | FK compuestas y claves por entidad; legacy no reasignado; no se escribió SQL |
| CONTRATO_API_MVP.md | router real/CORS/modelo/criterios | prefijo verificado, nuevas rutas propuestas, PATCH necesita CORS; replay estable |
| REQUISITOS_Y_CRITERIOS_ACEPTACION.md | contratos/plan | 17 requisitos y 38 criterios únicos con precondición/acción/resultado/evidencia/tipo |
| MATRIZ_COMPATIBILIDAD_IMPLEMENTACIONES.md | commits y contratos | clasifica null/ownership/catálogo/V2; no compatibilidad de ejecución afirmada |
| PLAN_IMPLEMENTACION_MVP.md | decisiones y AC | ruta crítica, dos personas y cuatro semanas de referencia, D-002 conservada |
| DECISIONES_PARA_APROBACION.md | contexto/CT-01–CT-08 | doce decisiones pendientes con campos de firma vacíos; no reabre D-001/D-002 |
| RESUMEN_PARA_DESARROLLADORES.md | maestro/modelo/plan | responde A–F sin atribuir aprobación; aclara mínimo selección vs vertical completa |
| FUENTES_Y_TRAZABILIDAD.md | diecinueve documentos antecedentes y código | procedencia, hashes, resultados históricos y contradicciones explícitos |

Verificaciones automáticas locales: doce Markdown presentes y UTF-8 legible; 30 encabezados numerados del maestro; 38 AC únicos; bloques JSON parseables; destinos de enlaces locales existentes. Los archivos versionados de backend/database/contexto/frontend se compararon por SHA-256 y los estados Git por snapshot, sin diferencias respecto al inicio, incluido el cambio local preexistente del diagrama. No se ejecutaron tests de aplicación, migraciones ni consultas NVD en esta misión.

Pendientes de revisión humana: conformidad de parser y filtros de versión NVD, límites oficiales vigentes, definición final de constraints para legado, cuotas/recuperación de worker y aceptación de todos los cambios de alcance. Estos pendientes no se disimulan como funcionalidades verificadas.
