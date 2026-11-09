# Decisiones para aprobación

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Registro para firma humana

Las doce decisiones están **PENDIENTE**. Recomendar no equivale a aprobar; los espacios vacíos no son consentimiento. Ninguna decisión modifica D-001 o D-002. Firmar por separado cada acuerdo, registrar fecha real y revisar cambios dependientes del contrato. Si se rechaza una recomendación, incrementar versión documental y recalcular dependencias antes de programar.

## DEC-01

- **Pregunta:** ¿Se aprueba este MVP reducido como primera fase y se aplaza Baseline?
- **Alternativas:** mantener alcance central completo; aprobar fase inventario/NVD; ampliar plazo.
- **Recomendación:** fase inventario/NVD explícita.
- **Justificación:** Dos personas/cuatro semanas y ausencia de ZAP seguro; evita confundir demo parcial con entrega anterior.
- **Riesgo:** Omitir un requisito establecido si no se formaliza recorte.
- **Impacto técnico:** Cambia criterio de finalización, no D-001/D-002.
- **Dependencias:** CT-01, RF-01–RF-10.
- **Consecuencia de posponer:** No se puede declarar completo el MVP reducido.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-02

- **Pregunta:** ¿Qué categorías iniciales y componente principal se admiten?
- **Alternativas:** routers/switches/servidores; solo web; ambos dominios.
- **Recomendación:** routers/switches/servidores, firmware/SO como identificación principal.
- **Justificación:** Conserva inventario existente; no requiere workspaces.
- **Riesgo:** Cobertura limitada a un componente.
- **Impacto técnico:** UI limitada y tipo libre conservado para históricos.
- **Dependencias:** DEC-01.
- **Consecuencia de posponer:** Modelo y formularios seguirán divergentes.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-03

- **Pregunta:** ¿Se aprueba propiedad individual de activos?
- **Alternativas:** created_by individual; workspaces; sin autorización por fila.
- **Recomendación:** propiedad individual con histórico sin dueño invisible.
- **Justificación:** Implementación propuesta 0002 y filtros ya disponibles.
- **Riesgo:** Acceso SQL directo no está aislado por RLS en V1.
- **Impacto técnico:** Aplicar 0002 solo tras coordinar aprobación/catálogo.
- **Dependencias:** D-002, RF-02.
- **Consecuencia de posponer:** Bloquea autorización de nuevas entidades.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-04

- **Pregunta:** ¿Un CPE principal por activo es suficiente para primera fase?
- **Alternativas:** uno principal; componentes múltiples; solo texto libre.
- **Recomendación:** uno principal confirmado, cobertura explícita.
- **Justificación:** Muestra de doce búsquedas ambiguas exige selección humana.
- **Riesgo:** No cubre todos los productos del equipo.
- **Impacto técnico:** Tabla única de identificaciones, sin componentes ahora.
- **Dependencias:** DEC-02/03.
- **Consecuencia de posponer:** No se puede cerrar cardinalidad ni contratos.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-05

- **Pregunta:** ¿Se aprueba historial por filas versionadas y sin borrado?
- **Alternativas:** sobrescritura; tabla única versionada; tabla más auditoría separada.
- **Recomendación:** tabla única versionada con reemplazo y motivo.
- **Justificación:** Preserva trazabilidad sin historial redundante.
- **Riesgo:** Crecimiento y control de cierre de fila.
- **Impacto técnico:** FK, índice de actual y claves por evento.
- **Dependencias:** DEC-04.
- **Consecuencia de posponer:** No se puede cambiar identificación sin riesgo de pérdida de evidencia.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-06

- **Pregunta:** ¿Se permite version=null y se bloquea evaluación específica hasta conocerla?
- **Alternativas:** mantener requerido; permitir null; usar marcador de texto.
- **Recomendación:** null al registrar, sin marcador ficticio; 409 en evaluación específica.
- **Justificación:** Guardar inventario sin inventar versión.
- **Riesgo:** Clientes actuales esperan string; edición no está implementada.
- **Impacto técnico:** SQL/ORM/Pydantic y clientes; editar versión requiere fase aprobada aparte.
- **Dependencias:** DEC-02/04.
- **Consecuencia de posponer:** Se bloquea contrato definitivo del formulario.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-07

- **Pregunta:** ¿Se aprueba potential/undetermined/not_applicable y una revisión humana?
- **Alternativas:** solo potential; ampliar estados; confirmación automática.
- **Recomendación:** tres estados, revisión única con evidencia; sin confirmed.
- **Justificación:** AND/rangos pueden necesitar condiciones no observadas.
- **Riesgo:** Errores de revisión humana y evaluador incompleto.
- **Impacto técnico:** CHECK, evidencia, revisor y concurrencia; no metodologías activas.
- **Dependencias:** DEC-04/05.
- **Consecuencia de posponer:** No se puede especificar seguimiento ni semántica de dashboard.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-08

- **Pregunta:** ¿Qué fragmentos NVD y cuánto histórico se conservan?
- **Alternativas:** solo CVE; snapshot acotado; espejo completo.
- **Recomendación:** snapshot pertinente acotado y conservar durante proyecto.
- **Justificación:** Reproducibilidad sin réplica completa; evidencia máximo64KiB.
- **Riesgo:** Retención no definida para operación a largo plazo.
- **Impacto técnico:** Metadatos/evidencia y aviso de truncamiento; revisión de privacidad.
- **Dependencias:** DEC-07.
- **Consecuencia de posponer:** No se cierra modelo de persistencia y pruebas.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-09

- **Pregunta:** ¿Workspaces y RLS V2 entran ahora?
- **Alternativas:** integrar V2; diferir; descartar definitivamente.
- **Recomendación:** diferir sin descartar evolución.
- **Justificación:** V2 cambia dominio y requiere contexto HMAC/API no integrados.
- **Riesgo:** Rehacer autorización futura si no se diseña evolución.
- **Impacto técnico:** No aplicar 004 al camino MVP; conservar propuesta de Rudi.
- **Dependencias:** DEC-01/03.
- **Consecuencia de posponer:** No hay baseline único para integración.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-10

- **Pregunta:** ¿Cómo ejecutar D-002 y acordar las rutas canónicas de este contrato?
- **Alternativas:** revisiones SQL y Alembic coordinadas; líneas independientes.
- **Recomendación:** Rudi SQL/bootstrap/grants/tests, Jesel ORM/Alembic y API; revisión cruzada.
- **Justificación:** D-002 ya APROBADA, esta decisión solo concreta operación sin reaprobarla.
- **Riesgo:** Drift de catálogo y dos caminos de migración.
- **Impacto técnico:** Manifiesto común y pruebas de nueva instalación/evolución.
- **Dependencias:** D-002, DEC-03/05/08.
- **Consecuencia de posponer:** Bloquea implementación coordinada, no modifica D-002.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-11

- **Pregunta:** ¿Se muestran parciales y qué presupuesto de consulta se acepta?
- **Alternativas:** ocultar parciales; mostrarlos; esperar indefinidamente.
- **Recomendación:** mostrar partial y avisos; presupuesto5páginas/5min propuesto.
- **Justificación:** Ejemplos históricos 2/5 y 2/521 no son totales recuperados.
- **Riesgo:** Recorte puede ocultar CVE relevantes, debe ser visible.
- **Impacto técnico:** Counters y warning_codes; cuota oficial a verificar antes de habilitar.
- **Dependencias:** DEC-08.
- **Consecuencia de posponer:** No se puede cerrar AC-D03/D06 ni aceptación de resultados.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________

## DEC-12

- **Pregunta:** ¿Actualización manual o automática y qué entrega móvil se exige?
- **Alternativas:** evaluación manual + móvil; scheduler; fase web previa con reducción explícita.
- **Recomendación:** evaluación manual por ejecución nueva y móvil mínimo online-first; scheduler/edición posterior.
- **Justificación:** No cambiar silenciosamente prioridad móvil central; evitar complejidad.
- **Riesgo:** Tiempo de dos clientes y versión desconocida no completable desde UI sin edición.
- **Impacto técnico:** Reparto/dispositivo/plazo; edición futura debe invalidar identificación actual.
- **Dependencias:** DEC-01/06/11, CT-02.
- **Consecuencia de posponer:** No se puede prometer actualización ni cierre de demo histórica.
- **Fase límite:** puerta T01, antes de trabajo dependiente; DEC-11 puede cerrarse antes de T09, DEC-12 antes de comprometer demo.
- **Estado actual:** PENDIENTE.
- **Jesel — aprobación/rechazo y observaciones:** ____________________
- **Rudi — aprobación/rechazo y observaciones:** ____________________
- **Fecha de aprobación conjunta (si existe):** ____________________
