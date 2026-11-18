> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Checklist de aceptación funcional para Jesel y Rudi

**PROPUESTA; aprobación humana PENDIENTE.** Las casillas no están marcadas por
las pruebas automáticas. Cada revisor registra su resultado y evidencia. Primero
acordar AA-D01–D08 del [registro de decisiones](04_DECISIONES_PENDIENTES.md).
No aprobar el MVP original únicamente porque pase el flujo web V2.

Versión revisada: Backend 4453ae0 / Frontend 4674cbc / Database a562e638;
hashes completos en el [manifiesto](06_MANIFIESTO_RC1.md).
Fecha/hora de sesión: __________. Operador: __________.
Alcance seleccionado y criterios obligatorios: __________.

## Preparación y revisión documental

| ID | Criterio observable de aceptación | Evidencia técnica disponible | Jesel | Rudi |
|---|---|---|---|---|
| CF-01 | Ambos identifican exactamente los cuatro hashes y el alcance que se evalúa | Manifiesto y referencias remotas verificadas ahora | [ ] | [ ] |
| CF-02 | Web 5177 / API 8004 / PG 55438 y volumen RC1 propios; desarrollo 5173/8000/55434 intacto | Verificación actual de entorno | [ ] | [ ] |
| CF-03 | Modo NVD fixture real y aviso visible; no abrir URLs/referencias externas | PID/env/fábrica MockTransport y navegador actual | [ ] | [ ] |
| CF-04 | Distinguir dispositivo V1, aplicación V2 y móvil pendiente | Matriz RF/RNF/38AC; decisiones de alcance | [ ] | [ ] |

## Recorrido funcional básico V2 (10–15 minutos)

Para observar creación/revisión **nuevas**, usar una cuenta ficticia nueva desde
la UI, con contraseña privada. Para inspección sin escrituras, usar el dataset
existente y marcar «evidencia previa revisada»; no simular un alta nueva ni volver
a revisar su hallazgo ya revisado. Toda escritura manual queda limitada a RC1.

| ID | Acción / resultado esperado | Relación con requisitos | Jesel | Rudi |
|---|---|---|---|---|
| CF-05 | Crear cuenta normal; no permitir campos internos, sin rol administrativo global | Alta V2; RNF-01 | [ ] | [ ] |
| CF-06 | Login valida identidad; workspace personal aparece con owner; dashboard vacío de esa cuenta | RF-02/07 | [ ] | [ ] |
| CF-07 | Crear aplicación con nombre/observaciones ficticias y URL de inventario; guardar no genera escaneo | RF-01 adaptado a V2, decisión AA-D01 | [ ] | [ ] |
| CF-08 | Agregar producto, fabricante y versión manualmente; no se afirma detección automática | Inventario V2 | [ ] | [ ] |
| CF-09 | Buscar RV340: candidato y versión visibles; selección requiere radio/botón explícitos | RF-03/04 | [ ] | [ ] |
| CF-10 | Confirmar CPE correcto; versión observada no se sustituye por una inventada | RF-04/05 | [ ] | [ ] |
| CF-11 | Solicitar consulta CVE y consultar su estado guardado; worker real sobre datos capturados | RF-05/06; contrato 201 V2 | [ ] | [ ] |
| CF-12 | CVE-2017-6784 conserva condiciones AND; resultado indeterminado, sin afirmar vulnerabilidad confirmada | AC-D05, RNF-05 | [ ] | [ ] |
| CF-13 | Guardar una revisión con motivo/evidencia que conserve undetermined; reviewed visible | RF-08; si dataset existente, solo inspeccionar su revisión | [ ] | [ ] |
| CF-14 | Crear/ver recomendación manual pending, diferenciada de mitigación realizada | Seguimiento V2 | [ ] | [ ] |
| CF-15 | Historial conserva ejecución/CPE/hallazgo/recomendación; dashboard coincide con el workspace y API | RF-06/07 | [ ] | [ ] |
| CF-16 | Cero hallazgos abiertos tras revisión se explica correctamente: el CVE sigue existiendo | Semántica de dashboard y aplicabilidad | [ ] | [ ] |
| CF-17 | Logout/login conserva datos; recarga exige login por sesión en memoria | RF-07, RNF-06 | [ ] | [ ] |
| CF-18 | Seguridad distingue declaración, desafío y control técnico; expiración/revocación no habilitan análisis | Preparación V2, no escaneo | [ ] | [ ] |
| CF-19 | Baseline disabled y sin ejecuciones/hallazgos ZAP inventados; verificación remota no disponible | Restricciones de esta revisión | [ ] | [ ] |

## Evidencia extendida (fuera del guion corto)

Puede revisarse la evidencia automatizada RC1 anterior sin repetir operaciones
destructivas. Si se exige nueva ejecución, programarla con perfiles/tmpfs propios;
no usar la base personal ni convertir esa ampliación en requisito ya firmado.

| ID | Resultado esperado / material que revisar | Procedencia | Jesel | Rudi |
|---|---|---|---|---|
| CF-20 | Dos cuentas no cruzan IDs, roles ni workspaces; pool no filtra contexto | Suites RC1 anteriores; lectura ajena 404 / propia 200 actual | [ ] | [ ] |
| CF-21 | 375/768/1280/1440, navegación, vacíos, carga, error/reintento y axe sin reglas omitidas | 9 E2E RC1 + repetición UX 4; capturas previas | [ ] | [ ] |
| CF-22 | Reinicio mantiene IDs/fechas/revisión; backup sensible privado y restore en otro volumen, aislamiento correcto | 13 comprobaciones y B tras reinicio/restore, misión RC1 anterior | [ ] | [ ] |
| CF-23 | Contratos V1/V2 no se mezclan: Asset.version nullable; Technology.version requerida;202 V1/201 V2 | Código/OpenAPI actual y matriz | [ ] | [ ] |
| CF-24 | 38 AC originales revisados, con brechas móviles/UI V1 explícitas; no se declaran todos cumplidos por una suite verde | Matriz de cobertura | [ ] | [ ] |
| CF-25 | Secretos, dumps, tokens y contraseñas no aparecen en la entrega | Escaneo previo RC1 y verificación del paquete actual | [ ] | [ ] |
| CF-26 | Defectos/riesgos clasificados; cualquier excepción está escrita con impacto y responsable por acordar | Registro de defectos/riesgos | [ ] | [ ] |
| CF-27 | Queda registrado qué funciones son obligatorias, cuáles se difieren y qué queda fuera del MVP | AA-D01–D07 | [ ] | [ ] |
| CF-28 | Ambos emiten un resultado individual y firman el alcance/hash exactos | AA-D08, acta siguiente | [ ] | [ ] |

## Regla de aprobación propuesta

Para **aceptar RC1 como candidato académico V2**: criterios obligatorios acordados
y evaluados, sin defecto S1/S2 abierto en ese alcance, excepciones menores
justificadas y resultado de ambos. Para **declarar versión académica definitiva**:
además deben cerrarse las decisiones de dominio, V1, móvil, workspaces y ZAP; si
alguna exige funciones ausentes, deben implementarse y comprobarse en otra versión.
Una firma de «revisado» no equivale a «aceptado».

| Revisor | Resultado: aceptada / con condiciones / rechazada / sin emitir | Alcance y excepciones | Fecha | Firma |
|---|---|---|---|---|
| Jesel | SIN EMITIR | __________ | __________ | __________ |
| Rudi | SIN EMITIR | __________ | __________ | __________ |

Defectos observados en sesión y pasos para reproducir: ____________________.
IDs de evidencia: ____________________. Decisiones AA-D resueltas: __________.
Conclusión conjunta: **PENDIENTE**. No crear tag ni marcar decisiones APROBADAS
automáticamente al llenar una sola casilla o al completar la demostración.
