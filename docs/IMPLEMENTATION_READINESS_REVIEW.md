# Preparación para implementación

**ESTABLECIDA:** equipo de dos, aproximadamente cuatro semanas; flujo prioritario móvil → API → PostgreSQL → web. **PROPUESTA:** esta evaluación, planificación y distribución, sujetas a aprobación. No hay código implementado ni garantías de plazo.

## Qué puede comenzar y qué bloquea

Puede comenzar sin decidir el stack: revisión de requisitos y criterios, diseño de pruebas con datos sintéticos, acordar escenarios de autorización, investigar compatibilidad y preparar decisiones. La implementación del flujo vertical depende de aprobar tecnología/plataformas, autenticación y acceso mínimos, esquema/API de creación y consulta, validaciones, idempotencia y persistencia. Las propuestas son revisables en [API](API_CONTRACT_DRAFT.md), [datos](DATA_DICTIONARY_DRAFT.md) y [criterios](ACCEPTANCE_CRITERIA.md).

Antes de comenzar implementación, el usuario debe resolver al menos:

1. **PENDIENTE:** stack y versiones, plataformas de desarrollo/despliegue y distribución móvil; confirmar PostgreSQL y D-002 como reparto de responsabilidad, sin cambiar D-002.
2. **PENDIENTE:** esquema mínimo de activo, contrato de creación/lectura, retención del formulario, reglas de fallo e idempotencia; establecer permisos y mecanismo de identidad.
3. **PENDIENTE:** modelo e historial de análisis/hallazgos, definición de estados y límites de datos; criterios para verificación humana y paginación parcial NVD.
4. **PENDIENTE:** responsable y evidencia de autorización de aplicaciones de prueba, lista permitida, control de red por conexión, aislamiento y prueba de rechazo. Si no se puede demostrar, bloquear ejecución ZAP.
5. **PENDIENTE:** actualización y eliminación de activos, métricas de dashboard, criterios medibles, plazos y reparto. Mantener esas operaciones sin habilitar hasta decidir.

## Dependencias y riesgos

Móvil depende del contrato mínimo; persistencia de esquema y transacciones; visualización web de consultas y permisos; NVD de selección manual válida y cuota; ZAP de autorización, destino y aislamiento verificable; dashboard de definiciones y consultas autorizadas. Riesgos: curva de aprendizaje, complejidad de ZAP/SSRF, latencia y cuota NVD, cambios en CPE, compatibilidad móvil, disponibilidad de dispositivos, ambigüedad de permisos, pérdida de respuesta y duplicados. Ninguna tecnología pendiente debe implementarse como si estuviera aprobada.

## Plan tentativo de cuatro semanas

| Semana | Actividades y trabajo paralelo PROPUESTA | Dependencias | Entregable y criterio de finalización PROPUESTA | Riesgo / reserva |
|---|---|---|---|---|
| 1 | Desarrollador A: especificar API, datos e identidad; B: flujos móviles/web y pruebas. Ambos: acordar stack y diseñar laboratorio de ZAP. | Decisiones 1–2 y acceso a entornos. | Contrato, modelo mínimo y pruebas aceptados; laboratorio propuesto revisado. | Falta de decisiones bloquea implementación; reservar tiempo de acuerdos. |
| 2 | A: backend/persistencia; B: móvil y consulta web; integrar diariamente. | Contrato y esquema aprobados. | Registro, fallo/reintento sin duplicados y lectura web pasan criterios AC-A–E. | Manejo de conectividad, migración y compatibilidad móvil. |
| 3 | A: cliente NVD, cuotas y manejo de datos; B: búsqueda/selección manual y presentación. | Flujo vertical, definición de CPE y hallazgos. | AC-F–I y L pasan con respuestas simuladas y datos de prueba. | Datos incompletos, páginas inestables, límite externo. |
| 4 | A: integración Baseline solo si controles aprobados y probados; B: resultados, dashboard, pruebas integrales; ambos revisan seguridad. | Lista, aislamiento, permiso de destino y pruebas negativas. | AC-J–L, límites de acceso y recuperación pasan en laboratorio; documentación de pendientes y demostración. | Si aislamiento no se demuestra, no ejecutar ni presentar ZAP como terminado; escalar alcance/plazo. |

Las letras identifican dos responsabilidades tentativas, no personas ni asignaciones aprobadas. La coordinación entre ambos es necesaria para contrato, seguridad y pruebas.

## Pruebas a preparar

**PROPUESTA:** pruebas de formulario y reconexión; persistencia transaccional con caída después de commit y reintentos concurrentes; validación de entradas y permisos por objeto; NVD vacía, parcial, cuota, timeout y sin CVSS con dobles; CPE obsoleto y versión ausente; páginas que cambian; ZAP con proceso y red simulados más prueba en laboratorio aislado autorizado de redirecciones, rebinding, puertos, IPv6 y revocación; protección de logs y reportes; dashboard sin datos no autorizados. Definir criterios cuantitativos y entorno antes de ejecución. No realizar pruebas en infraestructura de terceros.
