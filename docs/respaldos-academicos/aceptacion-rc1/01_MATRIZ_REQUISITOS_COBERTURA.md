> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Matriz de requisitos y cobertura real de RC1

**Estado documental: PROPUESTA para aceptación. Estado de aprobación conjunta:
PENDIENTE.** Cobertura técnica y aprobación de alcance son columnas distintas.
Los códigos RF/RNF/AC siguientes conservan los del contrato, no son decisiones
nuevas. AA-D identifica únicamente las preguntas de este paquete.

## Fuentes y precedencia

Todas las fuentes de Context se inspeccionaron en
`caa7ef13b0e2b24848991e32b3c0ee3fc59139c7`:

- **S1:** contrato maestro 0.2.0 (referencia local no incluida),
  §§6–7, 21–23 y CT-01/02/04; contiene 10 RF, 7 RNF y 38 AC propuestos.
- **S2:** registro individual de aprobación (referencia local no incluida),
  MVP-001–MVP-013. Jesel aprobó alcance/principios, Rudi no ha ratificado. No es
  aprobación en bloque de los 38 AC ni de cada contrato técnico.
- **S3:** requisitos históricos (referencia local no incluida):
  flujo móvil→API→web y ZAP Baseline como antecedentes; el aplazamiento de ZAP
  tiene apoyo individual de Jesel, no aprobación conjunta inventada.
- **S4:** decisiones globales (referencia local no incluida):
  solo D-001/D-002 APROBADAS. Este paquete no modifica ese registro.
- **S5:** Backend RC1 `4453ae0d9b25de361947113f7c61c1b4797385ab`: OpenAPI,
  esquemas, modelos, `app/api`, pruebas y `docs/evidence/V2_RC1_RESULTS.json`.
- **S6:** Frontend RC1 `4674cbcd9f46ad0e9e587b0db630b9f51a990a86`:
  `src/app/App.tsx`, páginas V2, componentes y pruebas RC1.
- **S7:** evidencia nueva de esta revisión (referencia local no incluida)
  y navegador/API (referencia local no incluida).

Los textos de S1 que describen el código al 8 de noviembre son instantáneas
históricas. Por ejemplo, **V1 RC1 ya admite `version=null` y tiene CPE/CVE**, aunque
el handoff antiguo los señalaba pendientes. La implementación posterior no
transforma por sí misma las propuestas de alcance en decisiones aprobadas.

## Cobertura de los RF originales

«API respaldada» significa código y pruebas versionadas, incluidas en la regresión
de RC1 anterior; no un nuevo pase manual firmado. «Parcial» identifica una
equivalencia incompleta o un cliente ausente. V1 se conserva en el mismo Backend,
pero la aplicación React montada es V2.

| ID / criterio original | V1 real en Backend RC1 | V2 real en RC1 | Cobertura académica y decisión |
|---|---|---|---|
| RF-01: inventario propio sin CPE, versión desconocida | `/api/v1/assets`, `AssetCreate.version` nullable; test `test_unknown_version_registration_confirmation_and_idempotency` | Aplicación puede existir sin tecnologías; añadir tecnología requiere versión conocida. No hay campos tipo de dispositivo/sitio equivalentes | API V1 respaldada; **parcial** en UI. Dispositivo ≠ aplicación. AA-D01/D02 |
| RF-02: listado/detalle por propietario | Propiedad individual `assets.created_by`, filtros y 404 | Membresías y RLS por workspace; cuenta nueva obtiene owner personal | Ambos implementados con modelos distintos; elegir política, AA-D03 |
| RF-03: candidatos oficiales, errores/paginación | Cliente NVD y `/api/v1/cpe-candidates` | Búsqueda dentro de aplicación con confirmación explícita | Implementado; demostración solo fixture. No prueba que el producto esté instalado |
| RF-04: identificación manual, revisión e historial | Eventos `asset_cpe_identifications`, confirmed/needs_review, reemplazo y motivo; API de historial | `technologies.selected_cpe`, comparación optimista y audit_event; snapshots de análisis | **Parcial frente al RF completo:** selección V2 probada, pero no hay UI/API de historial detallado de identificaciones equivalente a V1. AA-D02/D06 |
| RF-05: consulta de identificación actual y específica | POST análisis202 con `identification_id`, versión suficiente y snapshot | POST análisis201 por tecnología confirmada, worker persistente | Implementado por contratos distintos. No intercambiar 201/202 ni IDs entre V1/V2 |
| RF-06: persistencia, cobertura y evidencia | Ejecuciones/hallazgos V1 con complete/partial/failed | Ejecuciones/hallazgos de aplicaciones, progreso y evidencia acotada | Implementado con límites; AND capturado produce undetermined, no confirmed |
| RF-07: historial y dashboard mínimo | Historial por API; componentes web heredados conservados | Historial/dashboard montados, recuentos contrastados con API | V2 implementado; UI V1 **pendiente de integración** si se exige evaluar dispositivos |
| RF-08: revisión única con motivo/evidencia | PATCH hallazgo con control de concurrencia | Revisión y recomendaciones manuales en UI/API | Implementado. Reviewed no significa vulnerable ni mitigado |
| RF-09: evitar duplicación y carreras | Idempotencia de registro/evento/análisis y transacciones | Claves en creaciones/análisis y comparación optimista en CPE | Implementado según cada operación; PUT CPE V2 no es el historial/replay de eventos V1 |
| RF-10: formulario/reconciliación ante respuesta perdida | Contrato API idempotente; pruebas de cliente/HTTP | Manejo de estados/intentos en cliente web online | **Parcial:** no se entrega cliente móvil ni se adjudica AC-A05 móvil por tener tests API |

## Cobertura no funcional y requisitos históricos adicionales

| ID / requisito | Evidencia real | Límite / clasificación |
|---|---|---|
| RNF-01: aislamiento, identidad activa y mínimos privilegios | SQL/RLS/API, cuentas A/B; lectura ajena404 y propia200 comprobadas ahora | Implementado y probado para el alcance ensayado; aprobación de política de workspaces pendiente |
| RNF-02: NVD desde Backend y origen fijo | Fábrica activa fixture, MockTransport CPE/CVE/worker; cero orígenes externos en navegador observado | Implementado; no se prueba NVD live en esta revisión |
| RNF-03: límites, timeout, cuotas/cobertura | Suites NVD/worker y resultados RC1 anteriores | Implementado con presupuestos; no se prometen SLA o catálogo exhaustivo |
| RNF-04: integridad, concurrencia e inmutabilidad | SQL, migraciones y pruebas de rollback/historia | Implementado según dominios; historial CPE V2 no equivale al evento V1 |
| RNF-05: fuente, fechas, selección y actor | Eventos V1, snapshots/auditoría V2 y evidencia de análisis | Implementado con límites de consulta de auditoría/UI; no existe API pública de lectura de toda la auditoría |
| RNF-06: cliente web/móvil online-first | Web responsive; JWT en memoria y error/reintento | Web implementada; **móvil nativo pendiente**. Responsive no lo sustituye |
| RNF-07: instalación/evolución reproducible | 755 pytest por ruta, SQL y 13 checks de recovery de misión RC1 anterior; aislamiento actual confirmado | Implementado para entorno local. No se repitieron migraciones/restores ahora |
| S3: registro móvil→API→web y conservación tras fallo | Backend preparado; no aplicación móvil demostrada | **Pendiente o recorte explícito**, AA-D04; requisito no eliminado por silencio |
| S3/S2: ZAP Baseline | Preparación de declaraciones/desafíos; verify no operativo, botón disabled | **Bloqueado**; Jesel apoyó aplazarlo. AA-D05 pide ratificación de la primera entrega, no habilitación |
| Objetivos cuantitativos de rendimiento/accesibilidad | Cuatro anchos y axe en RC1; sin SLA aprobado | **Pendiente** definir rúbrica; no inventar tiempos garantizados |

## Funciones V2 adicionales y exclusiones

| Función | Estado de implementación | Relación con MVP original |
|---|---|---|
| Registro normal y workspace personal transaccional | Implementado y probado | Alta de usuario nueva; no administrador global |
| Aplicaciones, URL y múltiples tecnologías | Implementado y probado | Dominio V2 adicional, fuera de la fase de dispositivos propuesta en S1; requiere AA-D01 |
| Workspaces y roles owner/editor/viewer | SQL/API implementados; sin gestión pública de membresías | Fuera de primera entrega apoyada individualmente por Jesel; AA-D03 debe resolver su inclusión |
| Recomendaciones manuales y estado pending | Implementado | Seguimiento V2; no remediación automática |
| Declaraciones y desafíos persistentes | Implementado con límites | Preparación de seguridad; nunca acreditan control técnico por sí solos |
| DNS/TLS fijado y gateway interno | Laboratorios 45/51 checks de misión RC1 | No equivalen a egreso público operativo ni habilitan ZAP |
| Edición/eliminación general de dispositivos, scheduler | No integrados como funciones del MVP de esta UI | Pospuestos/fuera de esta fase propuesta; aprobación posterior si se requieren |
| CISA KEV, Redis, PDF del producto, QR/cámara, GPS, offline completo | No entregados | Exclusiones explícitas históricas, no defectos. La documentación de revisión no es una función de exportar PDF del producto |
| Active Scan, escaneo de routers/redes, explotación, secretos de clientes | No habilitados | Prohibidos/fuera de alcance; no deben entrar en RC2 por inferencia |

## Trazabilidad de los 38 AC del contrato original

Esta tabla no declara «38 aceptados». Resume apoyo técnico y brecha para la
aceptación humana. Familias de evidencia en S5: **T-A**=`test_mvp_cpe.py`,
**T-B**=`test_z_mvp_b.py`, **T-M**=`test_mvp_migrations.py` y
`test_mvp_b_migrations.py`, **T-N**=pruebas unitarias NVD/CVE,
**T-S**=`test_client_contract.py`/`test_browser_http_contract.py`,
**T-V2**=`test_v2_registration.py`/`test_v2_stabilization.py`;
**UI-RC1**=`tests/e2e/rc1` de Frontend. Son archivos existentes en los commits
fijados; la suite verde no sustituye una prueba exacta de cada texto del AC.

| AC | Apoyo localizado / cumplimiento técnico | Brecha o pase formal requerido |
|---|---|---|
| AC-A01 | AssetCreate y propiedad V1; T-A/T-S | Mostrar UI de dispositivo si AA-D02 la exige |
| AC-A02 | Extras prohibidos; propietario del servidor, T-A | Revisores validan campos del contrato elegido |
| AC-A03 | Registro concurrente/idempotente, T-S | Revisar evidencia de concurrencia; no repetir en base personal |
| AC-A04 | V1 nullable ya implementado; T-A unknown_version | No trasladarlo a TechnologyCreate V2, que exige versión |
| AC-A05 | Manejo de reintentos web y API como apoyo | **Parcial:** falta cliente móvil; pase de respuesta perdida en cliente acordado |
| AC-B01 | Candidatos y selección explícita; T-A/T-N/UI-RC1 | Demo actual contiene un candidato; no demuestra ahora fixture de varios candidatos |
| AC-B02 | Búsqueda vacía y persistencia separadas, T-N/T-A | Revalidar caso sin candidatos en aceptación extendida |
| AC-B03 | Errores NVD seguros, T-A/T-N | No confundir timeout/error con total cero |
| AC-B04 | Paginación del cliente NVD, T-N | Verificar presupuesto del contrato elegido, no cuota oficial actual |
| AC-C01 | Evento V1 con fuente/actor/motivo; T-A | V2 PUT no ofrece el mismo evento completo de identificación |
| AC-C02 | Resolución/deprecado, T-A/T-N | Confirmación no se inventa ante fallo del catálogo |
| AC-C03 | Autorización antes de NVD/replay, T-A | V2 tiene membresía/RLS; mantener separados ambos modelos |
| AC-C04 | Confirmación concurrente e índice único, T-A | Revisar evidencia SQL; no adjudicarla al historial V2 |
| AC-C05 | confirmed→needs_review→nuevo evento, T-A | **Parcial en UI RC1:** no monta ese flujo V1 |
| AC-C06 | Replay e inconsistencia de clave, T-A | No equiparar replay del POST V1 con PUT V2 |
| AC-D01 | Versión desconocida/identificación no actual bloquean V1, T-A/T-B | Elegir dominio y conservar sus precondiciones |
| AC-D02 | API V1 acepta202/idempotencia; T-B | V2 usa201 por contrato propio; no es defecto de código |
| AC-D03 | Complete/partial y páginas, T-B | El fixture de demo no cubre todos los presupuestos |
| AC-D04 | Cero válido frente a failed, T-B/T-N | No interpretar ninguno como certificación de seguridad |
| AC-D05 | AND y evidencia compleja, T-N/UI-RC1 | Dataset y revisión actual siguen undetermined |
| AC-D06 | Cuotas/retry, T-B/T-N | Operación NVD live excluida de esta aceptación |
| AC-D07 | Snapshot tras cambiar identificación, T-B | Indicación «identificación previa» en UI V1 no montada: **parcial para AC completo** |
| AC-E01 | Unicidad de CVE por ejecución, T-B | No sumar ejecuciones como CVE distintos globales |
| AC-E02 | Rollback del reemplazo CPE, T-A | Evidencia API/SQL V1, no nuevo ensayo actual |
| AC-E03 | Fallo de última página conserva parciales, T-B | Revisar log de suite RC1 como evidencia previa |
| AC-E04 | Historial/snapshots, T-B y UI V2 | Histórico de activo V1 por UI sigue pendiente si obligatorio |
| AC-E05 | Revisión única y origen conservado, T-B/UI-RC1 | Revisión ya guardada visible ahora; firma humana pendiente |
| AC-E06 | Conflicto de revisión concurrente/obsoleta, T-B | No intentar revisar de nuevo el dataset preparado |
| AC-F01 | Listado/detalle por propietario, T-S | V2 usa workspace; aprobar su política |
| AC-F02 | IDs mezclados y recursos ajenos, T-A/T-B/T-V2 | Comprobación actual404/200 complementa, no sustituye matriz completa |
| AC-F03 | Usuario inactivo/token, T-B/T-S/T-V2 | No probar desactivación sobre usuarios personales |
| AC-F04 | Host fijo, validación y redirects, T-N/T-S | Mock activo; no tráfico a objetivo externo |
| AC-F05 | Redacción/headers y verificación previa de secretos, T-S | No constituye auditoría universal nueva de logs/dependencias |
| AC-F06 | Bytes/evidencia acotados, T-N/T-B | Preservar avisos de truncamiento; no prometer evidencia ilimitada |
| AC-F07 | Evolución/rollback y catálogos, T-M | Probado en misión RC1, sin migraciones nuevas en esta revisión |
| AC-F08 | Vertical web V2 probada | **Pendiente como AC original móvil/dispositivo→misma web**; AA-D01/D02/D04 |
| AC-F09 | CORS/PATCH, T-S/T-V2 | Mantener identidad y origen exacto, no wildcard |
| AC-F10 | Recuperación de worker/páginas, T-B | Recovery anterior de datos completados no es por sí solo prueba de caída con trabajo running |

La recomendación es aceptar formalmente **un alcance explícito**, no calcular un
porcentaje mezclando V1 API, V2 UI y móvil ausente. Las decisiones AA-D01–D08 y
las casillas del checklist deben quedar registradas por ambos revisores.
