# Contrato de consulta CVE

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Precondiciones y flujo

Autenticar usuario activo, autorizar activo y verificar que `identification_id` pertenece al activo, es el evento actual y tiene state=confirmed. La versión observada debe ser conocida para esta consulta específica: 409 VERSION_REQUIRED ante null. El usuario confirma el alcance principal; no equiparar versión comercial con CPE. Si part/version del CPE no permiten filtro específico, responder 409 IDENTIFICATION_INCOMPLETE; no sustituir por una búsqueda vaga. CPE hardware con versión NA exige revisión explícita de revisión/plataforma y nunca acredita firmware; el primer MVP puede bloquearlo como no suficientemente específico hasta verificar esa ruta de API. Preferir firmware o SO identificable.

Crear ejecución queued con copia inmutable del CPE y versión observada, parámetros y FK a identificación. La selección se comprueba bajo bloqueo de assets al aceptar, y queda congelada aunque después cambie. Responder 202 tras persistir aceptación. Un worker del mismo backend toma tareas queued bajo bloqueo; no Redis ni nuevo broker. La reanudación tras caída no reacepta la misma clave; tareas running sin finalización se cierran al recuperar el proceso: completed/partial con aviso INTERRUPTED si existen páginas válidas persistidas y estado consistente, failed con coverage null si no existen; una tarea ya finalizada no se modifica y el usuario puede solicitar nueva ejecución con otra clave. La política operativa debe probarse antes de habilitar 202; no usar memoria como única cola.

NVD solo desde backend: HTTPS y host fijo, `/rest/json/cves/2.0` con cpeName codificado y paginación `startIndex/resultsPerPage`. Parámetros de rangos/virtualMatchString no se usan en MVP hasta verificación oficial adicional. No esperar red con transacción SQL abierta. Política de presupuesto propuesta (no cuota oficial): un request cada 7 s, cola global única, máximo 5 páginas de 100 elementos y 5 minutos por ejecución, timeout externo 15 s, hasta 2 reintentos con retroceso para transitorios; límite efectivo siempre menor o igual a cuota oficial vigente, a verificar antes de ejecutar cliente productivo. 403/429 detienen solicitudes nuevas de la ejecución y respetan Retry-After si existe; no eludir restricciones. Cache acotada para catálogo, no réplica NVD; consulta manual de actualización crea ejecución nueva.

## Cobertura independiente de aplicabilidad

| Persistencia | API/dashboard | Condición |
|---|---|---|
| queued, coverage=null | pendiente | aceptada sin respuesta externa |
| running, coverage=null | en curso | trabajo iniciado, sin promesa de totalidad |
| completed, coverage=complete | consulta completa | todas las páginas verificadas de parámetros fijados, sin truncamiento ni cambios de total detectados |
| completed, coverage=partial | consulta parcial | al menos una página válida, pero límite/fallo posterior o incertidumbre de paginación |
| failed, coverage=null | fallida | no hay página válida completa o error de procesamiento impide publicar resultados |

Una página válida con total=0 puede completar la consulta sin hallazgos; no significa activo seguro. Un resultado JSON malformado no se convierte en total cero. `retrieved_count` cuenta entradas procesadas; `stored_count` cuenta CVE distintos persistidos; `total_results` es total observado NVD (nullable antes de respuesta); conservar progress y warning_codes. Si cambian totalResults u orden entre páginas, no asegurar snapshot de NVD: partial con aviso. `complete` se refiere a consulta, no a cobertura universal de vulnerabilidades.

**Evidencia histórica, no reejecutada:** RV340 firmware 1.0.0.33, 1/1; TL-SG108E firmware 1.1.2, 2/5; Ubuntu Linux 10.04 LTS, 2/521. Las dos últimas fueron muestras parciales. En TL-SG108E el CVE incluía AND firmware/hardware; vulnerable=false en nodo hardware no descarta el CVE, puede expresar entorno necesario.

## Semántica de resultado y seguimiento

- CVE recuperado: dato devuelto por consulta, todavía no conclusión del sistema.
- `potential`: hay criterio relevante al producto/versión, sin incompatibilidad demostrada; no confirma vulnerabilidad ni explotación.
- `undetermined`: condiciones/versión/configuración faltantes o evaluador no soportado; UI «requiere revisión».
- `not_applicable`: solo revisión humana con evidencia suficiente y motivo; conservar hallazgo y dato original.
- `confirmed` no forma parte del vocabulario de aplicabilidad del MVP. Una metodología externa podría estudiarse después, sin asignarla por NVD.

Separar `findings.status` (open/reviewed) de `findings.applicability`. AND requiere todas las condiciones; OR admite alternativas; negación y rangos deben preservarse. El MVP no implementará un evaluador universal: ante configuración compleja no verificada, marcar undetermined y presentar evidencia, nunca simplificar con contains ni comparar versiones por orden lexicográfico. Rangos inclusivos/exclusivos y UUID de criterio se guardan tal como se reciben; no asumir semver en todos los fabricantes.

## Persistencia y actualización

Reutilizar analysis_runs y findings. Una ejecución hace upsert controlado por `(analysis_id,source,external_id)`; JSON de evidencia acotado incluye configuraciones pertinentes, identificadores de criterio, timestamps, estado NVD y referencias HTTPS no ejecutables. Límite de 64 KiB por evidencia; si requiere truncamiento, conservar aviso y marcar undetermined. No introducir campos de procedencia dentro de cvss: guardar CVSS original en su columna y metadatos en evidence. Transacciones por página válida; cobertura solo se completa al terminar. No publicar complete ante rollback de última página.

La revisión necesita expected_updated_at para controlar concurrencia y motivo/evidencia acotada; en MVP solo transición open→reviewed. El resultado de ingesta original se conserva en evidence.initial_applicability, revisión se guarda en review_by/reviewed_at/review_reason/review_evidence; no habilitar revisiones repetidas sin un historial de revisiones aprobado. Nueva evaluación siempre genera otro análisis. No borrar histórico ni reescribir el efecto de una selección anterior. Retención: conservar registros de ejecución/revisión durante el proyecto, limpieza únicamente con política posterior aprobada. Los resultados de origen no se presentan como españoles originales: textos externos pueden estar en otro idioma, la interfaz sí comunica estados en español.
