# Plan maestro de implementación

**PROPUESTA:** ruta y nombres de módulos ilustrativos sujetos a revisión humana; los archivos sugeridos no existen en los repositorios de implementación inspeccionados. No se aprueban tecnologías ni operaciones al incluirlas aquí. **ESTABLECIDA:** flujo prioritario móvil → API → PostgreSQL → web; registro y consulta de activos, CPE asistido, CVE potenciales, dashboard y ZAP Baseline restringido forman parte del alcance. Consulta la [auditoría real](REPOSITORY_READINESS.md) y [trazabilidad](REQUIREMENTS_TRACEABILITY.md).

El orden propuesto construye una vertical mínima desde la semana 1 y no pospone la integración móvil: el contrato y los permisos se acuerdan antes de trabajar en paralelo, pero las interfaces pueden probarse contra dobles durante el desarrollo. ZAP requiere una prueba temprana de viabilidad de aislamiento; su ejecución solo se habilita al demostrar controles. Las duraciones son aproximadas; ningún hito implica aprobación o garantía de plazo.

## Puertas de decisión

- **G0 PENDIENTE:** stack/entornos/versiones, ubicación del móvil, flujo de ramas, identidad y alcance de permisos, formulario, esquema de activo, contrato `POST/GET`, validaciones/errores y conservación del formulario. Sin ello no comenzar la implementación dependiente.
- **G1 PENDIENTE:** política de idempotencia (clave, actor/ruta, huella de cuerpo, concurrencia, expiración), modelo de datos y D-002 aplicado a la solución elegida. Propuesta: clave transaccional; alternativa: reconciliación manual por intento (más simple, riesgo de duplicados y trabajo humano). Recomendar clave si se puede ensayar contra PostgreSQL.
- **G2 PENDIENTE:** política NVD: selección, CPE sin correspondencia/versión, paginación, límites, cobertura, persistencia y CVSS. Verificar fuente oficial CVE 2.0 y cuotas; [investigación previa](EXTERNAL_INTEGRATIONS.md) registra límites de acceso.
- **G3 PENDIENTE:** responsable y evidencia de autorización de destinos de prueba, lista y revocación; restricción de salida, DNS, redirecciones y aislamiento demostrados. Si la barrera no pasa pruebas negativas, no ejecutar ZAP.
- **G4 PENDIENTE:** métricas de dashboard, reglas de hallazgos y retención, criterios de entrega y pruebas cuantitativas. La actualización y eliminación de activos siguen pendientes; no habilitarlas por figurar en el borrador de API.

## Fases dependientes

### F0 — Acordar el mínimo y planificar trabajo

- **Objetivo / repositorio:** revisar G0–G4 en contexto; no editar los repositorios de aplicación durante esta misión.
- **Módulos sugeridos:** contratos y decisiones en `context` ya existentes; ubicación móvil a decidir.
- **Tareas:** resolver G0/G1; elegir cómo se probarán G2/G3; descomponer aceptación AC-A–L; documentar contrato de integración compartido.
- **Dependencias:** requisitos, restricciones y equipo disponibles. **Resultado/aceptación:** persona autorizada acepta decisiones esenciales y ejemplos de solicitud, respuesta y errores; las decisiones restantes tienen responsable de resolución.
- **Pruebas:** revisar ejemplos, criterios y permisos por recurso con ambos desarrolladores. **Riesgo:** formular propuestas como aprobación o iniciar código antes de G0.

### F1 — Preparar bases de desarrollo y prueba

- **Objetivo / repositorios:** establecer herramientas locales reproducibles en backend, frontend y database, además de móvil en ubicación aprobada.
- **Módulos sugeridos:** archivos de dependencias y guía de ejecución propios de la tecnología que se apruebe; en database, configuración PostgreSQL y datos sintéticos. No se fija nombre ni estructura.
- **Dependencias:** G0. **Tareas:** acordar versiones, comandos, variables sin secretos, entornos separados, pruebas y colaboración; no desplegar producción.
- **Resultado/aceptación:** cada desarrollador puede preparar entorno de prueba y ejecutar una comprobación mínima con instrucciones revisadas; servicios reales de cliente no son necesarios.
- **Pruebas:** arranque y limpieza de entorno aislado, conexión de prueba, verificación de comandos. **Riesgo:** incompatibilidad de versiones o dispositivo móvil no disponible.

### F2 — Persistencia y API de activos

- **Objetivo / repositorios:** registrar y leer activos en backend y database; **APROBADA** D-002 asigna modelos SQLAlchemy y migraciones Alembic a backend si se adopta ese stack, y configuración/seed/documentación a database.
- **Módulos sugeridos:** backend: módulo de activos, validación, repositorio, migraciones y pruebas; database: configuración e inicialización de prueba. Son nombres funcionales, no rutas existentes.
- **Dependencias:** F1, G0/G1, permisos y esquema mínimos. **Tareas:** validar campos, autorización por recurso, crear y consultar, transacción y manejo de duplicados/errores; seeds solo sintéticos.
- **Resultado/aceptación:** `POST` y `GET` acordados conservan campos y rechazan entradas o accesos indebidos; reintento idéntico tras respuesta perdida no crea segundo activo bajo la política aprobada.
- **Pruebas:** contratos, base de datos real de prueba, rollback, carrera de claves e idempotencia; AC-C/D. **Riesgo:** cambios de contrato/modelo y transacciones parciales.

### F3 — Móvil y consulta web: primera vertical

- **Objetivo / repositorios:** crear formulario móvil y lectura web conectados al backend; ubicación móvil **PENDIENTE**.
- **Módulos sugeridos:** móvil: pantalla de formulario, cliente API y conservación temporal; frontend: listado/detalle y cliente API; pruebas de ambos. Nombres ilustrativos.
- **Dependencias:** G0 y contrato; F2 para integración real. **Tareas:** implementar en paralelo con dobles, integrar al terminar F2; retener todos los valores tras fallo y reutilizar clave de reintento si se aprueba; no crear sincronización offline completa.
- **Resultado/aceptación:** registrar desde móvil, confirmar persistencia y ver mismo activo vía API en web; fallo de red conserva formulario y reintenta; permisos respetados.
- **Pruebas:** AC-A/B/E, desconexión, respuesta perdida, formularios opcionales y prueba en dispositivo accesible. **Riesgo:** ubicación móvil, almacenamiento local y conectividad no acordados.

### F4 — CPE y resultados NVD

- **Objetivo / repositorios:** backend y frontend: búsqueda de candidatos, selección humana y consulta CVE con presentación de resultados potenciales.
- **Módulos sugeridos:** cliente NVD, limitador de llamadas, servicio de consultas y hallazgos en backend; búsqueda y resultados en frontend, con dobles de API.
- **Dependencias:** F2/F3 y G2. **Tareas:** verificar contrato oficial vigente, gestionar cuotas y paginación, preservar fuente/fecha/cobertura y distinguir sin candidato, sin CVE, parcial y fallo.
- **Resultado/aceptación:** usuario elige CPE, CVE visibles como potenciales y no se muestran fallos como ausencia de vulnerabilidades; sin espejo NVD.
- **Pruebas:** AC-F–I/L, versión ausente, candidato obsoleto, timeout, cuotas, sin CVSS y página incompleta. **Riesgo:** comportamiento NVD y límites no verificados previamente.

### F5 — ZAP Baseline controlado

- **Objetivo / repositorios:** backend y frontend: solicitar, consultar y mostrar análisis de aplicación de prueba previamente autorizada.
- **Módulos sugeridos:** lista/validación de destino y ejecutor aislado en backend, pantalla de solicitud y resultados en frontend; almacenamiento y configuración sujetos a decisión.
- **Dependencias:** G3, F2 y contrato/roles. **Tareas:** elegir solo ID de destino controlado por servidor; revalidar autorización; restringir conexiones, recoger informe mínimo; sin Active Scan, URLs arbitrarias ni dispositivos físicos.
- **Resultado/aceptación:** solo objetivo y conexiones autorizados, proceso aislado, estado y alertas consultables; si controles fallan no se ejecuta. Códigos ZAP de alerta no se clasifican automáticamente como fallo técnico.
- **Pruebas:** AC-J/K/L, revocación, redirect, DNS rebinding, IPv4/IPv6, puertos y reporte malformado, solo en laboratorio autorizado. **Riesgo:** el control de salida no puede demostrarse; entonces se bloquea la entrega de ZAP, no se elimina el requisito.

### F6 — Dashboard, integración y entrega

- **Objetivo / repositorios:** frontend y backend, más móvil y database para prueba completa.
- **Módulos sugeridos:** consultas de resumen, pantalla de dashboard, pruebas E2E y guías de ejecución. **Dependencias:** F3–F5 y G4.
- **Tareas:** calcular métricas autorizadas con fecha y alcance, verificar retención, seguridad, integración, documentación y revisión cruzada.
- **Resultado/aceptación:** demostración completa de la vertical, CPE/NVD, ZAP autorizado y dashboard; sin datos de usuarios no autorizados. **Pruebas:** [estrategia](TEST_STRATEGY.md), evidencias de AC-A–L y verificación de no regresión. **Riesgo:** tiempo insuficiente; escalar plazo/alcance a usuario, sin marcar terminado lo no probado.

## MVP frente a mejoras aplazables

**ESTABLECIDA:** flujo de registro/consulta, selección CPE y consulta NVD, resultados, dashboard y Baseline seguro son parte del alcance. La actualización detallada y eliminación están **PENDIENTE**, no son CRUD aprobado. **PROPUESTA:** diferir filtros y métricas adicionales, optimizaciones y variantes de UI no necesarias para criterios acordados; una funcionalidad excluida o cambio de alcance requiere autorización explícita. La ausencia de ZAP seguro deja incompleto el MVP acordado, aunque la vertical de activos pueda demostrarse.
