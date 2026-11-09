# Modelo de datos conceptual

> **Actualización de estado — 2026-11-08:** Jesel aprobó individualmente el alcance del MVP de inventario/CPE/NVD sin ZAP ni workspaces operativos en primera entrega; ratificación de Rudi y detalles técnicos siguen PENDIENTES. Véanse [registro de aprobación](MVP_APPROVAL_RECORD.md) y [contrato consolidado](VULNTRACK_MVP_CONTRACT.md). El contenido anterior que sigue se conserva como antecedente; no constituye aprobación conjunta ni habilita ZAP. D-001 y D-002 permanecen intactas.

**PROPUESTA:** se han identificado cuatro entidades conceptuales: `User`, `Asset`, `AnalysisRun` y `Finding`. Sus nombres no establecen atributos, relaciones ni comportamientos definitivos. El [diagrama](../diagrams/data_model.mmd) muestra únicamente nodos independientes.

**PENDIENTE:** atributos, tipos de datos, relaciones, cardinalidades, restricciones, índices, reglas de integridad y estrategia de identificación. No existe información suficiente para especificarlos.

**APROBADA:** D-002 asigna los modelos SQLAlchemy y las migraciones Alembic a backend, y la configuración de PostgreSQL, inicialización, seed y documentación propia a database. La adopción de esas tecnologías permanece **PROPUESTA**.
