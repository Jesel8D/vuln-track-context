# Resumen para Jesel y Rudi

**PROPUESTA PARA REVISIÓN — NO APROBADO.** Versión documental 0.1.0, 6 de noviembre de 2026. Ningún contrato nuevo se declara implementado. D-001 y D-002 conservan su estado APROBADA.

## Recomendación para ambos desarrolladores

Construir una **fase propuesta** de inventario manual de routers, switches y servidores con un componente principal (firmware/SO/software/hardware) identificado mediante un CPE confirmado, consulta NVD, resultados potenciales o indeterminados y revisión humana documentada. No se aprueba nada por consolidar documentos. Primero resolver la reducción frente al alcance central con Baseline y la entrega móvil prioritaria.

**A. ¿Hay información para aprobar formulario?** Sí, para decidir sobre el formulario propuesto: cinco campos obligatorios pasan a cuatro más versión nullable, opcionales actuales conservados y selección tecnológica posterior. El backend hoy no acepta version null. No hace falta aprobar todas las funciones futuras para evaluar el formulario, pero su implementación depende de DEC-02/03/06 y del contrato de esquema.

**B. ¿Qué aprobar antes de implementar?** DEC-01–DEC-08 (alcance, categorías, propiedad, cardinalidad CPE, historial, versión, aplicabilidad y persistencia), concretar D-002 en DEC-10, y decidir parcialidad/actualización y móvil DEC-11/12 antes de prometer sus hitos. DEC-09 debe fijar que V2 no sea dependencia accidental. D-001/D-002 siguen aprobadas.

**C. ¿Cambio mínimo Database V1?** Para selección: una tabla `asset_cpe_identifications` versionada, version nullable y coordinación de created_by (0002 no está en V1). Para vertical completa: ampliar analysis_runs/findings con identificador, evidencia, counters, revisión y claves/índices/grants. No basta una columna selected_cpe ni una tabla de historial duplicada. Son propuestas, no SQL ejecutado.

**D. ¿Qué backend reutilizar?** Login/JWT/current_user, filtro por propietario, registro/listado/detalle, normalización, transacciones/SQLAlchemy, idempotencia exclusiva de activos, errores seguros, entorno Docker y Alembic verificado. Añadir servicios y schemas, adaptar null y CORS PATCH; no afirmar compatibilidad de catálogo sin probarla.

**E. ¿Qué V2 posponer?** Workspaces, membresías, aplicaciones/targets, tecnologías por aplicación, duplicados de análisis/hallazgos, HMAC/RLS colaborativo, recomendaciones/imports/auditoría de ese dominio. Aprovechar conceptos de FK compuestas y separación de tecnologías; no aplicar 004 ni convertir activos físicos en aplicaciones. No se descarta el trabajo de Rudi.

**F. ¿Qué implementar primero?** Tras firmas, Rudi concreta SQL/grants/índices e integridad; Jesel concreta schemas, ORM/Alembic y autorización del contrato. Juntos cierran fixtures y formulario móvil/web para vertical. Luego candidatos, confirmación/historia, consulta CVE y resultados/revisión. Cuatro semanas son referencia, no garantía; ante capacidad insuficiente se revisa alcance/plazo antes de declarar terminado.

Ocho operaciones nuevas canónicas están en CONTRATO_API_MVP.md; consulta actual e historial comparten GET cpe-identifications con scope. 38 criterios AC medibles distinguen pruebas históricas de nuevas. Ninguna suite ni consulta NVD se reejecutó en esta misión.

## Lectura recomendada

1. Documento maestro: visión, campo/API y reglas integradas.
2. Decisiones: firmar o rechazar por separado, sin consentimiento implícito.
3. Modelo/API/criterios: revisión cruzada y puerta de implementación.

Estado local conservado: backend 2cb124d limpio; Database V1 b493fd1 con modificación previa del diagrama; contexto d481061 limpio; frontend sin commits. Referencias origin/rudi disponibles localmente, sin fetch. Detalles de fuentes/contradicciones en FUENTES_Y_TRAZABILIDAD.md.

## Documentos del paquete

- [CONTRATO_API_MVP.md](CONTRATO_API_MVP.md)
- [CONTRATO_CONSULTA_CVE.md](CONTRATO_CONSULTA_CVE.md)
- [CONTRATO_IDENTIFICACION_CPE.md](CONTRATO_IDENTIFICACION_CPE.md)
- [DECISIONES_PARA_APROBACION.md](DECISIONES_PARA_APROBACION.md)
- [FORMULARIO_ACTIVOS_DEFINITIVO_PROPUESTO.md](FORMULARIO_ACTIVOS_DEFINITIVO_PROPUESTO.md)
- [FUENTES_Y_TRAZABILIDAD.md](FUENTES_Y_TRAZABILIDAD.md)
- [MATRIZ_COMPATIBILIDAD_IMPLEMENTACIONES.md](MATRIZ_COMPATIBILIDAD_IMPLEMENTACIONES.md)
- [MODELO_DATOS_PROPUESTO.md](MODELO_DATOS_PROPUESTO.md)
- [PLAN_IMPLEMENTACION_MVP.md](PLAN_IMPLEMENTACION_MVP.md)
- [REQUISITOS_Y_CRITERIOS_ACEPTACION.md](REQUISITOS_Y_CRITERIOS_ACEPTACION.md)
- [RESUMEN_PARA_DESARROLLADORES.md](RESUMEN_PARA_DESARROLLADORES.md)
- [VULNTRACK_MVP_CONTRACT_PROPOSAL.md](VULNTRACK_MVP_CONTRACT_PROPOSAL.md)
