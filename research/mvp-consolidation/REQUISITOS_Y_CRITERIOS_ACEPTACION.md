# Requisitos y criterios de aceptación

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

| ID | Requisito propuesto |
|---|---|
| RF-01 | Registrar inventario propio sin CPE, version nullable tras adaptación. |
| RF-02 | Consultar listado/detalle con aislamiento por propietario. |
| RF-03 | Buscar candidatos CPE verificables con errores y paginación. |
| RF-04 | Confirmar manualmente identificación y registrar revisión/historial. |
| RF-05 | Aceptar consultas CVE de identificación actual confirmada y específica. |
| RF-06 | Persistir resultados con estado, cobertura y evidencia. |
| RF-07 | Consultar ejecuciones/hallazgos anteriores y mostrar dashboard mínimo. |
| RF-08 | Revisar una vez hallazgos con motivo y evidencia. |
| RF-09 | Evitar duplicación por reintentos y carreras. |
| RF-10 | Conservar formulario y reconciliar respuestas perdidas en cliente. |
| RNF-01 | Aislamiento, usuario activo, mínimo privilegio y no secretos. |
| RNF-02 | Solo backend consulta host NVD fijo, sin destinos arbitrarios. |
| RNF-03 | Cuotas, tamaño, timeouts y cobertura explícita. |
| RNF-04 | Integridad transaccional, inmutabilidad y concurrencia controlada. |
| RNF-05 | Trazabilidad de fuente, fecha, selección y actor. |
| RNF-06 | Cliente web/móvil online-first; sin sincronización offline completa. |
| RNF-07 | Reproducibilidad de instalación y evolución en base desechable. |

## Criterios verificables

| ID | Requisito | Precondiciones | Acción | Resultado esperado | Evidencia | Tipo y cobertura actual |
|---|---|---|---|---|---|---|
| AC-A01 | RF-01 | usuario A activo y esquema válido | POST activo con datos válidos | 201, persistencia única y propietario A | HTTP+fila; test_authenticated_registration_sets_owner_and_rejects_manipulation | integración; histórica parcial |
| AC-A02 | RF-01/RNF-01 | A autenticado | enviar created_by=B o campo extra | 422, cero filas creadas | contrato y conteo | integración; histórica |
| AC-A03 | RF-09 | mismo usuario/clave/cuerpo, dos conexiones | enviar simultáneamente | un activo, mismo recurso, rollback perdedor | test_authenticated_concurrency y conteos | integración; histórica |
| AC-A04 | RF-01 | A activo | registrar version=null y sin CPE | 201 tras adaptación; GET identificación pending | nuevo test API+SQL | integración nueva |
| AC-A05 | RF-10 | formulario completo, corte de envío simulado | fallar envío y reintentar misma intención | valores conservados y misma clave, sin nuevo activo | prueba cliente y API | E2E nueva |
| AC-B01 | RF-03 | fixture con varios candidatos NVD | buscar producto | cada candidato conserva UUID/nombre y ninguno seleccionado | fixture de catálogo y captura UI | unidad+UI nueva |
| AC-B02 | RF-03 | fixture NVD total=0 | buscar | 200 items=[]; activo permanece; sin confirmed | HTTP/DB | integración nueva |
| AC-B03 | RF-03/RNF-03 | fixture timeout/429/JSON inválido | buscar | 504/503/502 según causa; no falsear total0 | doble HTTP y logs seguros | unidad nueva |
| AC-B04 | RF-03 | 20+ candidatos en fixture | pedir segunda página dentro límites | offset correcto y total del origen sin afirmar totalidad | doble HTTP y assertions | unidad nueva |
| AC-C01 | RF-04/RNF-05 | candidato vigente validado y A dueño | confirmar con expected_current_id correcto | 201 evento, UUID/nombre/fuente/actor/fecha/motivo guardados | SQL+HTTP | integración nueva |
| AC-C02 | RF-04 | UUID inexistente o deprecated | confirmar | 409 CPE_UNAVAILABLE, sin evento confirmado | doble NVD+DB | integración nueva |
| AC-C03 | RF-04/RNF-01 | activo de A, usuario B activo | confirmar o leer historial | 404, sin solicitud NVD ni cambio de datos | logs de doble y snapshots | seguridad nueva |
| AC-C04 | RF-04/RF-09 | dos cuerpos distintos, mismo current_id | confirmar concurrentemente | una fila current, otra 409; cadena sin ramas | dos sesiones reales y UNIQUE | concurrencia nueva |
| AC-C05 | RF-04 | identificación confirmed | marcar needs_review y luego confirmar otra | historia conserva tres eventos, análisis previo intacto | filas, replaces_id y tiempos | integración nueva |
| AC-C06 | RF-09 | clave ya usada en confirmación | repetir cuerpo y luego variarlo | 201 mismo evento para replay;409 para otro cuerpo | DB y respuesta inmutable | integración nueva |
| AC-D01 | RF-05 | identificación no confirmada, antigua o version null | solicitar análisis | 409 y cero ejecuciones nuevas | HTTP+conteo | integración nueva |
| AC-D02 | RF-05/RF-09 | identificación válida y versión conocida | POST análisis, luego repetir clave | 202 mismo id, una ejecución queued | SQL+HTTP | integración nueva |
| AC-D03 | RF-06 | fixture NVD 1/1 y luego 2/5 con presupuesto | procesar consulta | complete en primer caso, partial en segundo y aviso | doble HTTP+estado DB | integración nueva |
| AC-D04 | RF-06 | fixture total0 válido vs error sin página | procesar ambos | completed complete sin hallazgos vs failed null | assertions de estado y cobertura | integración nueva |
| AC-D05 | RF-06 | fixture AND hardware+firmware, rangos no soportados | correlacionar | preservar condiciones; undetermined, nunca confirmed | snapshot de configuración y resultado | unidad nueva |
| AC-D06 | RNF-03 | limitador global y fixture429 | agotar presupuesto | sin ráfaga ni evasión; backoff/aviso y partial o failed | reloj simulado y conteo solicitudes | unidad nueva |
| AC-D07 | RF-05 | aceptación válida, cambia identificación después | procesar ejecución previa | snapshot original se mantiene y UI indica identificación previa | histórico y UI | integración nueva |
| AC-E01 | RF-06/RF-09 | mismo CVE repetido entre páginas | persistir en misma ejecución | una fila por análisis/source/external_id | UNIQUE y conteos | SQL nueva |
| AC-E02 | RNF-04 | fallo inducido al cerrar/insertar identificación | confirmar | rollback total: fila antigua sigue current | SQL real y excepción inducida | integración nueva |
| AC-E03 | RNF-04 | última página falla al persistir | procesar | nunca complete; rollback de página sin pérdida previa | SQL+estado ejecución | integración nueva |
| AC-E04 | RF-07 | dos ejecuciones con misma CVE y distinta selección | consultar anteriores | ambas accesibles, sin mezclar evidencia ni sumar como CVE únicos | GETs+UI | integración nueva |
| AC-E05 | RF-08 | hallazgo open y fecha vigente | revisión con evidencia | reviewed y actor/fecha/motivo, initial_applicability conservado | GET y SQL | integración nueva |
| AC-E06 | RF-08 | hallazgo ya revisado o timestamp obsoleto | PATCH revisión | 409, sin segunda revisión ni sobreescritura | SQL antes/después | concurrencia nueva |
| AC-F01 | RF-02/RNF-01 | A/B y activo de A | B lee detalle/listado | 404 en detalle y exclusión en listado/total | test_owner_filtered_pagination_and_totals | integración; histórica |
| AC-F02 | RNF-01 | A/B, análisis y hallazgo de A | B prueba UUIDs y rutas mezcladas | 404 para todo, sin evidencia ajena | matriz API y conteos | seguridad nueva |
| AC-F03 | RNF-01 | usuario desactivado y token previo | replay/GET/operación nueva | 401, ningún acceso externo ni escritura | test_inactive_user_cannot_replay_or_read + ampliación | histórica parcial+nueva |
| AC-F04 | RNF-02 | URL arbitraria y controles en query | enviar input malicioso | 422 o codificación segura, solo host permitido | doble cliente y prueba de redirect | seguridad nueva |
| AC-F05 | RNF-01 | tokens/notas sensibles sintéticos | forzar errores y revisar logs | ningún secreto de prueba ni cuerpo en logs/respuestas | captura logs con valores centinela | seguridad nueva |
| AC-F06 | RNF-03 | respuesta externa >límite o evidencia>64KiB | ingerir | abortar respuesta sobredimensionada; evidencia truncada con aviso/undetermined | fixture y aserciones | unidad nueva |
| AC-F07 | RNF-07 | V1 legado sintético y ruta nueva | ensayar migración/aprobación catálogo | históricos intactos sin propietario inventado, FK/grants válidos | informe aislado y comparación hashes | migración nueva |
| AC-F08 | RF-07/RNF-06 | cliente móvil/web y backend real aislado | registro→selección→consulta→resultados | mismo activo visible, cobertura y potencial visibles, sin escaneo | grabación E2E y consultas DB | E2E nueva |
| AC-F09 | RNF-03 | nueva ruta PATCH y origen permitido | preflight y PATCH autenticado | preflight permitido, sin bypass de identidad | ampliación test_cors +HTTP | contrato nueva |
| AC-F10 | RF-05/RNF-04 | worker cae con tareas running | reiniciar proceso en entorno desechable | running huérfano termina partial si conserva páginas válidas o failed si no; queued recuperable; sin duplicación | estados antes/después y pruebas de worker | integración nueva |

«Histórica» significa test existente inspeccionado y resultado anterior documentado; no ejecución nueva ni garantía ante cambios. Backend docs/TEST_RESULTS.md informa 77 unitarias, 122 suite PostgreSQL y 29 bootstrap, no sumables. Esta misión no ejecuta suites. Todos los criterios nuevos están PENDIENTES; no se aprobó un umbral de rendimiento de producción. Para cerrar hito exigir 100% de los criterios de la capacidad elegida y cero fugas en matriz de dos usuarios; si se recorta capacidad debe aprobarse alcance antes de declararla satisfecha.
