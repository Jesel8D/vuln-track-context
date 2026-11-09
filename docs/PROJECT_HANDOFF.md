# VulnTrack — estado para retomar

Registro 2026-11-08. **Contrato del MVP aprobado por Jesel, pendiente de ratificación por Rudi.** Solo alcance y principios/conformidades expresas en MVP_APPROVAL_RECORD.md; detalle técnico y acuerdo global PENDIENTES. No se habilita producción.

## Producto y contrato

Inventario manual de routers, switches y servidores, un CPE principal confirmado por humano, consulta NVD/CVE con condiciones y evidencia, visualización/seguimiento de resultados potenciales. Sin escaneo de dispositivos, workspaces operativos ni ZAP operativo en primera entrega según Jesel. Posponer Baseline cambia el alcance previo: documentación antigua conservada, ratificación Rudi pendiente. No se aprobó recortar prioridad móvil→API→web.

Leer [contrato maestro](VULNTRACK_MVP_CONTRACT.md), [registro individual](MVP_APPROVAL_RECORD.md), [matriz de decisiones técnicas antecedente](../research/mvp-consolidation/DECISIONES_PARA_APROBACION.md), [investigación](../research/nvd-cpe/RESUMEN_EJECUTIVO.md), [auditoría antecedente](../research/architecture-audit/AUDITORIA_EJECUTIVA.md). No interpretar estados históricos como aprobaciones actuales. Los 31 informes están respaldados con manifiesto; URLs de consulta y evidencia histórica no son reconsultas actuales.

## Ramas y capacidades

| Repositorio | Rama / commit | Estado real |
|---|---|---|
| Backend | feat/backend-v1-assets / 2cb124d7cc41e22a62a305760826240f9208c257 | FastAPI, PostgreSQL/ORM, JWT/Argon2id, propiedad individual propuesta, idempotencia, CORS GET/POST, pruebas y Docker; CPE/CVE aún no implementados |
| Backend | rudi / 1a7ec7b6b13612339f14b94850119638660514c6 | README y gitignore complementarios; no backend alternativo |
| Database | feat/postgresql-database-v1 / b493fd17e9290ca94fa085d49230073ed1837e1a | cinco tablas, SQL/grants/seed y pruebas; no assets.created_by en baseline |
| Database | rudi / 54b7644467a1096c011daf3e5c23d7f9a7773f93 | V2 experimental: workspaces/aplicaciones/RLS/HMAC, no integrar automáticamente |
| Database | audit/database-v2-security-validation / 2c9723146e910aa51211958556961b4da7019ce3 | correcciones de tests e informe de validación incompleta; publicada independiente |
| Contexto | dev/jesel / d481061e5ecb53b9a033086481add37f04d8c549 | base histórica, sin merge de esta formalización |
| Contexto | rudi / 6c8baf74a3d7bcd7a0a9ebcca2dda0d797cbfeeb | documentación de propuesta V2, preservada |
| Contexto | docs/mvp-context-consolidation | aprobación parcial, contrato e investigaciones; hash recuperable en inventario/reporte de publicación |
| Frontend | main sin commits; remoto sin ramas al verificar | no implementación funcional demostrada, no se inicializó ficticiamente |

Arquitectura de implementación: clientes futuros→FastAPI→PostgreSQL; NVD futuro solo backend. V2 es una alternativa separada, no arquitectura vigente. 0002 del backend propone propiedad y preserva históricos sin dueño; no se ejecuta al arrancar. Registro actual exige version string; null y los ocho endpoints nuevos son propuesta. API existente: login/me, POST/GET assets y GET detalle, además health/ready. Nuevos CPE/análisis/revisión exigen esquema/catálogo/CORS coordinados.

## Pruebas y límites

Backend docs/TEST_RESULTS.md registra históricamente 77 unitarias, 122 suite PostgreSQL y 29 bootstrap, con solapamiento: no sumar. Database V1 documenta 55 aserciones. Rama de auditoría posterior registra 55 V1,15 V2,25 adversariales, nueve repetidas de preparación; no completa la matriz de seguridad. Esta sincronización no reejecutó suites ni migraciones.

Investigación histórica:12 búsquedas con candidatos múltiples; CVE1/1 Cisco,2/5 TP-Link,2/521 Ubuntu; dos consultas parciales, sin verificar equipo real. Selección manual y no afirmar explotación. Cuotas/filtros oficiales de NVD requieren verificación antes de cliente operativo.

## Riesgos y trabajo pendiente

Ratificación de Rudi y aprobación de detalles; contrato version nullable; invariantes de identificación/historia e idempotencia nueva; CPE correcto y rangos/AND/OR; cuota/recuperación de worker; evolución segura SQL/Alembic/manifiesto; UI móvil/web por construir. RLS V2 no protege V1 y la API no establece HMAC/workspace. No usar como prueba de seguridad de integración.

Parche local de diagrama respaldado como pendiente, no aplicado ni comprometido en Database: elimina username del dibujo aunque la columna SQL existe. Leer research/local-preservation/README.md antes de recuperarlo. Material-estudio y sus temporales permanecen locales según inventario, no son prerrequisitos de desarrollo ni respaldo completo de esa carpeta.

## Distribución flexible y siguientes tareas

Jesel y Rudi pueden trabajar en cualquier repositorio; los nombres en el plan son orientativos. D-002 distribuye artefactos: Database configura PostgreSQL/bootstrap/seed/documentación; Backend mantiene ORM y Alembic. Revisar juntos contratos y cambios de esquema.

Primero: ratificar alcance y detalles, sin aprobación inventada. Después: preparar rama SQL/ORM/API desde líneas compatibles y probar en PostgreSQL desechable. Por último: primera vertical con registro, selección y resultados autorizados. Seguir [SETUP_NEW_PC.md](SETUP_NEW_PC.md) para recuperar y ejecutar pruebas; no aplicar V2 ni migraciones persistentes automáticamente.
