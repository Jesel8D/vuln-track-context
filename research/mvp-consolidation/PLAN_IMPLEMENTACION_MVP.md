# Plan de implementación para Jesel y Rudi

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Ruta crítica y puertas

DEC-01–DEC-08/DEC-11 → contrato de propiedad/formulario → SQL + ORM/Alembic coherentes → registrar/leer → identificación confirmada e historial → consulta NVD con cobertura → persistencia/revisión → demo móvil/web. DEC-09/DEC-12 definen exclusiones/actualización; D-002 no se reabre por DEC-10. No comenzar migración dependiente sin aprobación humana del diseño. Las estimaciones son relativas: S≈medio día ideal, M≈1–2 días, L≈3–4; no compromisos ni suma de capacidad disponible. Las tareas compiten con integración y aprendizaje de clientes.

| Tarea / semana | Responsable propuesto | Prioridad | Dependencia | Estimación | Entregable y criterio |
|---|---|---|---|---|---|
| T01 revisar decisiones y CT-01/02 /1 | ambos | P0 | lectura fuentes | S | firmas o rechazo explícito en decisiones, alcance sin contradicción |
| T02 contrato JSON/modelos y fixtures /1 | Jesel, revisión Rudi | P0 | T01 | M | AC-A04/B01 y schemas revisados |
| T03 SQL aditivo, grants/índices y datos ficticios /1 | Rudi, revisión Jesel | P0 | T01/T02 | M | diseño de identificación/histórico e integridad AC-E02/F07 |
| T04 ORM, Alembic y catálogo /1–2 | Jesel, revisión Rudi | P0 | T03 | M | instalación/evolución aislada reproducible, AC-F07 |
| T05 formulario móvil mínimo y consulta web /1–2 | ambos con reparto acordado | P0 | T02 | L | AC-A05/F08 vertical actual; frontend vacío implica trabajo nuevo |
| T06 NVD CPE, validación de UUID y límite global /2 | Jesel | P0 | T02/T04 | M | AC-B01–B04, fuente oficial de cuotas comprobada antes de habilitar |
| T07 historial/confirmación concurrencia /2 | Jesel + Rudi tests SQL | P0 | T04/T06 | M | AC-C01–C06 |
| T08 pantalla de candidatos e identificación /2 | compartido | P0 | T05/T06/T07 | M | selección humana y estado pending/needs_review visibles |
| T09 worker mínimo y consulta CVE /3 | Jesel | P0 | T07 | L | AC-D01–D07/F10, presupuesto y fallos explícitos |
| T10 evidencia/únicos/revisión SQL /3 | Rudi propone SQL, Jesel ORM/Alembic | P0 | T03/T09 | M | AC-E01/E03/E05/E06, snapshots sin cambios |
| T11 resultados/dashboard básico /3–4 | ambos | P0 | T08/T09/T10 | M | AC-E04/F08, no sumar potenciales como confirmados |
| T12 seguridad/regresión y demo /4 | ambos revisión cruzada | P0 | T05–T11 | L | todos criterios del flujo acordado; cero fugas ni pérdida de historial |
| T13 métricas adicionales/edición versión /posterior | por acordar | P2 | DEC-12 | M | contrato y migración separados si se aprueba |

Rudi NO asume propiedad del directorio Alembic: entrega SQL/bootstrap, privilegios y pruebas; Jesel materializa revisiones Alembic en backend. Los dos revisan hashes/manifiestos y compatibilidad. Backend administra cola SQL y secretos operativos, no Database. Frontend y móvil no son gratis: en T01 acordar reparto diario y dispositivo accesible; no posponer móvil silenciosamente frente al requisito central. Si no cabe, aprobar fase reducida o plazo antes de construir y nunca declarar completo el MVP previo sin evidencia.

Puertas de aceptación semanales: semana1 AC-A01–A04/F01 con contrato aprobado; semana2 B/C y formulario con conservación; semana3 D/E con fixtures; semana4 F y demo real aislada. Todos los criterios son propuestos y deben ejecutarse tras implementar. Pruebas externas reales mínimas se reservan para smoke autorizado; CI usa fixtures públicos acotados y Postgres desechable. No escáneres, no producción.
