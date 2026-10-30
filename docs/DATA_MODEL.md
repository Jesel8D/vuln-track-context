# Modelo de datos conceptual

**PROPUESTA:** se han identificado cuatro entidades conceptuales: `User`, `Asset`, `AnalysisRun` y `Finding`. Sus nombres no establecen atributos, relaciones ni comportamientos definitivos. El [diagrama](../diagrams/data_model.mmd) muestra únicamente nodos independientes.

**PENDIENTE:** atributos, tipos de datos, relaciones, cardinalidades, restricciones, índices, reglas de integridad y estrategia de identificación. No existe información suficiente para especificarlos.

**APROBADA:** D-002 asigna los modelos SQLAlchemy y las migraciones Alembic a backend, y la configuración de PostgreSQL, inicialización, seed y documentación propia a database. La adopción de esas tecnologías permanece **PROPUESTA**.
