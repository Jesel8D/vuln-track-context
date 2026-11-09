# Aprobación individual del MVP — Jesel

**APROBADO POR JESEL PARA EL MVP PROPUESTO.** Fecha de registro: 2026-11-08. **Ratificación de Rudi: PENDIENTE. Estado global del acuerdo de equipo: PENDIENTE.** No se redefine APROBADA/APPROVED ni se alteran D-001 o D-002.

Fuente: solicitud explícita «MISIÓN MAESTRA: VULNTRACK — FORMALIZACIÓN DEL MVP Y SINCRONIZACIÓN SEGURA CON GITHUB», recibida en este chat. No hay confirmación individual de Rudi documentada. La autorización de commit/push no equivale a aprobación técnica conjunta.

> VulnTrack permitirá registrar manualmente routers, switches y servidores, identificar un CPE principal mediante confirmación humana, consultar vulnerabilidades conocidas en NVD y dar seguimiento a resultados potencialmente aplicables, sin escaneo de dispositivos, sin workspaces operativos y sin ZAP operativo durante la primera entrega.

El registro JSON existente solo representa decisiones globales y no prevé firmas individuales: `context/decisions.json` permanece byte a byte intacto, con D-001 y D-002. Este documento complementa la procedencia sin introducir aprobaciones globales nuevas. La cita aprueba alcance y principios; campos, rutas, constraints, presupuestos y criterios técnicos no se dan por aprobados en bloque.

| Identificador | Recomendación | Estado individual Jesel | Evidencia/límite | Rudi | Estado global |
|---|---|---|---|---|---|
| MVP-001 | Alcance: registro manual, CPE confirmado, NVD/CVE y seguimiento | APROBADO POR JESEL PARA EL MVP PROPUESTO | Cita expresa del alcance en esta solicitud. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-002 | Categorías: routers, switches y servidores | APROBADO POR JESEL PARA EL MVP PROPUESTO | Cita expresa y estado individual explícito. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-003 | Propiedad individual mediante assets.created_by | CONFORMIDAD EXPRESA CON LA PROPUESTA | La solicitud dice «Jesel: conforme con la propuesta»; no equivale a migración aprobada para producción. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-004 | Un CPE principal confirmado por activo | CONFORMIDAD EXPRESA CON LA PROPUESTA | La solicitud declara conformidad y la cita recoge CPE principal humano. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-005 | Historial de identificaciones CPE | PROPUESTA; confirmación técnica individual específica pendiente | Se registra recomendación; no se infiere firma de su esquema detallado. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-006 | Versión desconocida al registrar | PROPUESTA; confirmación técnica individual específica pendiente | La solicitud registra recomendación; null aún no aceptado por backend. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-007 | CVE potenciales, condiciones y revisión | APROBADO POR JESEL PARA EL PRINCIPIO DEL MVP | La cita expresa dice resultados potencialmente aplicables; estados técnicos concretos siguen propuesta. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-008 | Persistir identificadores, procedencia, evidencia, fechas y cobertura | PROPUESTA; ratificación técnica pendiente | No se asume aprobación del esquema/retención/límites específicos. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-009 | Sin workspaces operativos durante primera entrega | APROBADO POR JESEL PARA EL MVP PROPUESTO | Exclusión expresa en cita. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-010 | Conservar V2 experimental sin fusión automática | DIRECTRIZ DE PRESERVACIÓN AUTORIZADA EN ESTA MISIÓN | No aprueba integración o despliegue de V2. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-011 | Identificar resultados parciales NVD | PROPUESTA; ratificación técnica pendiente | Registrar recomendación, sin aprobación conjunta inferida. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-012 | Consultas NVD bajo demanda | PROPUESTA; ratificación técnica pendiente | No hay autorización para scheduler por este registro. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |
| MVP-013 | Posponer ZAP Baseline a fase posterior | APROBADO POR JESEL PARA EL MVP PROPUESTO | Cambio explícito de alcance frente a requisitos anteriores, pendiente ratificación Rudi. | PENDIENTE DE RATIFICACIÓN | PENDIENTE |

## Cambio respecto al contexto previo

MVP-013 pospone Baseline, incluido en REQUIREMENTS e IMPLEMENTATION_MASTER_PLAN anteriores. Se conserva íntegra esa documentación; la primera entrega propuesta por Jesel excluye ZAP operativo, pero el cambio conjunto queda pendiente de Rudi. Las restricciones de una futura fase (solo Baseline, web autorizada, aislamiento y salida verificados, sin escaneo de routers/redes) siguen vigentes. No desaparece por ello el antecedente ni se declara completo el alcance anterior.

No se documentó aprobación de sustituir el flujo móvil→API→web: debe mantenerse como prioridad o acordarse un recorte explícito. D-001 preserva contexto independiente; D-002 distribuye responsabilidades por tipo de artefacto, no limita quién puede colaborar en cada repositorio.

## Ratificación futura

Rudi: ____________________ Fecha: ____________________ Observaciones/decisiones ratificadas: ____________________

Cambios de detalle aceptados por Jesel: ____________________ Fecha: ____________________

DEC-01–DEC-12 del paquete de consolidación son identificadores antecedentes y no equivalen automáticamente a MVP-001–MVP-013. Usar ambos con su prefijo; no renumerar ni rellenar firmas históricas.

SHA-256 del texto de solicitud fuente: `42ba60d0746c0026a6af5db6a07b058b957e3f00f1a918fb55d35942605b0690`. El adjunto no se publica; se conserva esta cita necesaria y la procedencia.
