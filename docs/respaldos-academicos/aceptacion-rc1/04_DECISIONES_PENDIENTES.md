> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Hoja de decisiones para Jesel y Rudi

**PENDIENTE en todos los puntos.** Las recomendaciones son **PROPUESTAS** de esta
revisión, no preferencias ya aceptadas. D-001/D-002 siguen APROBADAS en Context y
no se reabren ni modifican. Los identificadores AA-D son locales a este paquete;
no sustituyen MVP-001–MVP-013 ni DEC-01–DEC-12 del contrato.

## Opciones, impactos y recomendación

| ID / pregunta | Opciones a decidir | Impacto y dependencias | Recomendación técnica propuesta |
|---|---|---|---|
| **AA-D01. Dominio de la primera entrega** | A: evaluar RC1 como piloto académico web V2; B: mantener dispositivos como entregable principal; C: exigir ambos dominios | A exige recorte/sustitución explícita del alcance histórico por ambos. B requiere UI de dispositivos/móvil acordado. C aumenta integración, pruebas y carga conceptual; no autoriza fusionar modelos | Revisar ya el piloto V2; no llamarlo MVP definitivo hasta decidir A/B/C. Si se elige A, dejar V1 preservado y el cambio contractual por escrito |
| **AA-D02. Dispositivos V1 en el producto evaluado** | A: API V1 conservada como legado fuera de la demo V2; B: incluir vertical V1 en un cliente separada de aplicaciones; C: encargar diseño posterior de integración | A no cumple por UI el MVP de routers/switches/servidores. B necesita rutas/pantallas/pruebas y respeta ownership V1. C requiere especificación y migraciones de ensayo futuras; no convierte devices en URLs | Para aceptar solo el candidato RC1, A con limitación explícita. Para declarar el MVP original completo, B o trabajo adicional acordado; ninguna conversión automática |
| **AA-D03. Política de workspaces** | A: conservar workspace personal y roles actuales, sin administrar membresías; B: exigir colaboración/gestión de miembros; C: exigir propiedad individual V1 sin workspace operativo | A coincide con la RC1 técnica, pero cambia MVP-009. B demanda API/UI y pruebas nuevas. C requiere elegir el flujo V1 o rediseño futuro; no basta ocultar el selector o apagar RLS | A para el piloto si ambos lo aprueban, especificando que owner no es administrador global. No habilitar membresías ni desactivar RLS en esta misión |
| **AA-D04. Cliente móvil y conservación de formulario** | A: mantener móvil mínimo online-first como obligatorio; B: diferirlo con acuerdo escrito y aceptar web responsive como único cliente de esta fase | A mantiene prioridad histórica y bloquea versión definitiva hasta implementarlo/probarlo. B es recorte expreso, no equivalencia entre responsive y móvil nativo | Decidirlo antes de aprobar funciones obligatorias. Si falta capacidad, B con pendiente y plazo por acordar; sin firma permanece A como requisito histórico no satisfecho |
| **AA-D05. ZAP en la primera entrega** | A: ratificar que queda fuera de la primera entrega; B: exigir ZAP para declarar definitiva la primera entrega | A ratifica el recorte apoyado por Jesel. B bloquea esa declaración y requiere otra misión con planificación y controles operativos de seguridad. Ninguna opción habilita ahora ZAP/verificación/egreso | A. Si se elige B, NO-GO de la entrega definitiva hasta implementar y probar los requisitos en una misión posterior autorizada. No activarlo como corrección de RC1 |
| **AA-D06. Funciones obligatorias y equivalencia V1/V2** | A: vertical V2 de CF-05–19 y controles de seguridad; B: además los 38 AC originales en el cliente/dispositivo; C: subconjunto enumerado con exclusiones justificadas | A conserva la brecha de historial detallado de identificaciones V1 y móvil. B demanda trabajo adicional. C necesita IDs exactos y no puede llamar cumplidos los excluidos | Definir lista expresa; para candidato V2, A más CF-01–04/20–28 revisados. Si se exige historial CPE completo V1, incluirlo explícitamente en RC2 |
| **AA-D07. Evidencia de evaluación y fixtures** | A: aceptar fixtures NVD identificados para reproducibilidad; B: exigir además prueba NVD live posterior controlada | A prueba integración, no actualidad NVD ni vulnerabilidad real. B necesita autorización, verificación de cuotas vigentes y entorno propio; no contacto a URLs objetivo | A para esta aceptación. Fijar capturas/semántica undetermined y límites. Cualquier prueba live queda fuera de la sesión y de esta misión |
| **AA-D08. Criterios y aprobación conjunta** | A: aceptación del candidato con condiciones concretas; B: rechazo para corrección/redefinición; C: aceptación académica definitiva solo con alcance y criterios completamente cerrados | A no equivale a C; B debe referir defectos/requisitos específicos. C exige ambos resultados, hashes, excepciones y evidencia, sin decisiones esenciales pendientes | Realizar revisión del candidato ahora; registrar por separado revisión y aceptación definitiva. Proponer cero S1/S2 abiertos en alcance acordado y excepciones menores explícitas |

## Registro individual (sin respuestas inferidas)

| ID | Estado de la decisión | Jesel: opción, observaciones, fecha | Rudi: opción, observaciones, fecha | Resolución conjunta / evidencia |
|---|---|---|---|---|
| AA-D01 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D02 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D03 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D04 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D05 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D06 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D07 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |
| AA-D08 | PENDIENTE | SIN EMITIR | SIN EMITIR | __________ |

En la sesión: elegir opciones, anotar impacto y dependencias, nombrar responsables
y capacidad real por acuerdo, y registrar una resolución fechada de ambos. Si
discrepan, mantener PENDIENTE y una lista de trabajo; no crear una aprobación por
mayoría de pruebas ni por ausencia de respuesta. Cualquier cambio futuro en
Context debe conservar la procedencia y los registros históricos; esta hoja no
los modifica. D-002 distribuye tipos de artefactos, no impide colaboración de
cualquiera de los dos desarrolladores en un repositorio.

Firmas de cierre de alcance: Jesel __________ Fecha __________;
Rudi __________ Fecha __________. Versión de la hoja y hashes aceptados: ______.
**Estado actual del cierre: PENDIENTE.**
