# VulnTrack — contexto maestro vigente

> Actualizado 2026-11-27. Este documento resume el estado observado en las ramas `main`; las ramas históricas y sus planes no se interpretan como implementación actual. El producto queda limitado a web por la decisión D-003. La ratificación técnica del MVP por el segundo desarrollador continúa pendiente.

## Producto y arquitectura

VulnTrack es una aplicación web para inventario, identificación asistida CPE y consulta de CVE potencialmente relacionadas desde NVD. La web llama a FastAPI; el backend centraliza autenticación, autorización, acceso a PostgreSQL y consultas externas. El navegador no accede directamente a la base. No se desarrollará una aplicación móvil (D-003). OWASP ZAP no está operativo ni forma parte del alcance actual del MVP.

El backend tiene una ruta real de consulta CVE a la API NVD 2.0. Se verificó una petición GET de solo lectura con un CPE de prueba, pero no una ejecución completa del botón desde la interfaz conectada a una instancia local. En instalaciones nuevas NVD live está deshabilitado por defecto: debe activarse deliberadamente en el backend y compilarse la web con `VITE_NVD_MODE=live`. La bandera pública de frontend no activa el backend.

## Repositorios en `main`

- [Backend](https://github.com/Jesel8D/vuln-track-backend): FastAPI, SQLAlchemy/Alembic, autenticación, autorización, inventario, servicios CPE/CVE, transporte NVD restringido y configuración Compose. No habilitar consultas live en producción hasta completar el control de egress y validar el host.
- [Frontend](https://github.com/Jesel8D/vuln-track-frontend): React, TypeScript, Vite y la interfaz web. Incluye vistas de inventario, tecnologías, identificación CPE e historial; algunos paneles CVE son demostrativos y dependen del modo de compilación.
- [Database](https://github.com/Jesel8D/vuln-track-database): PostgreSQL, inicialización, roles, políticas, semillas y pruebas SQL. Los modelos ORM y migraciones Alembic corresponden al backend por D-002.
- [Context](https://github.com/Jesel8D/vuln-track-context): decisiones, requisitos, arquitectura, instrucciones, auditorías y documentación compartida.

Los cuatro repositorios contienen `logo.png` y `preview.png` en su raíz. Backend, Frontend y Database ya contienen MIT y `.gitignore`; Context también los tiene. El About lateral de GitHub (descripción/topics) aún requiere que el propietario lo edite: la sesión conectada no dispone de permiso de administración. Las propuestas están en [GITHUB_ABOUT.md](GITHUB_ABOUT.md).

## Seguridad y validación observadas

La [revisión de seguridad del 27-11-2026](SECURITY_CONTROLS_REVIEW_2026-11-27.md) registra los controles revisados, correcciones, pruebas y limitaciones. Entre los controles observados hay Argon2id/JWT, filtros de autorización, consultas SQL parametrizadas, validación de entradas, límites de HTTP, aislamiento y permisos PostgreSQL, transporte NVD con TLS y fijación del host, y cabeceras de seguridad.

En esa revisión pasaron 260 pruebas backend seleccionadas (5 pruebas de laboratorio se excluyeron por dependencias Unix), y 181 pruebas frontend además de tipos, lint y build. Los audits Python y npm no reportaron vulnerabilidades conocidas. Esto no es un pentest. No se hizo DDoS, carga contra el despliegue, inyección SQL/XSS contra una instancia en ejecución, ni escaneo de las nuevas imágenes finales.

## Gates que siguen abiertos

1. Instalar/arrancar Docker y ejecutar pruebas SQL/migraciones únicamente en un proyecto desechable, verificando versión vigente, esquema limpio y migración V1 con datos de prueba.
2. Escanear las imágenes exactas de producción con Docker Scout o Trivy y conservar la salida fechada.
3. Aplicar un filtro de egress a nivel de host/gateway que permita NVD de forma controlada, incluyendo IPv4/IPv6 y comportamiento fail-closed.
4. Decidir y probar cuota de abuso compartida para registro/login si habrá varias réplicas; diseñar revocación individual de JWT antes de exponer el servicio con mayor riesgo.
5. Verificar configuración real del host, firewall, certificado, monitoreo, secretos, respaldos cifrados y restauración.
6. Ejecutar pruebas de seguridad y carga limitadas solo contra un entorno aislado autorizado; no realizar DDoS contra producción.
7. Actualizar la documentación antigua por repositorio y cerrar discrepancias señaladas en el registro vigente.
8. Solicitar al dueño del repositorio que complete los About laterales de GitHub.

El estado por repositorio, evidencias y dependencias externas está en [REMAINING_WORK_2026-11-27.md](REMAINING_WORK_2026-11-27.md). El proyecto sigue **NO-GO para despliegue público** hasta resolver los gates que requieren infraestructura y revisión humana.

## Aprobaciones

D-001 y D-002 definen el repositorio de contexto y la separación de responsabilidades de Database/Backend. D-003 establece el alcance web-only a petición explícita del usuario el 2026-11-27. El registro individual del MVP de 2026-11-08 conserva su alcance y procedencia; no se infiere ratificación técnica conjunta ni se habilita ZAP. Consultar [decisiones](DECISIONS_AND_OPEN_QUESTIONS.md) y [registro de aprobación](MVP_APPROVAL_RECORD.md).
