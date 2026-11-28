# Revisión de controles de seguridad — 27 de noviembre de 2026

## Alcance y límite de esta revisión

Revisión estática y documental de los repositorios Backend, Frontend, Database y Context. Se inspeccionaron autenticación, autorización, validación, consultas SQL, integración NVD, configuración de red y Docker, almacenamiento de sesión y límites operativos. No se ejecutó una prueba de penetración, no se enviaron solicitudes a activos, no se iniciaron servicios y no se volvió a escanear imágenes o dependencias. Trivy, Gitleaks, Bandit y pip-audit no están instalados en esta PC; Docker y npm están disponibles, pero no se usaron para iniciar servicios ni consultar registros de vulnerabilidades.

Esta revisión no sustituye una auditoría independiente ni permite afirmar una calificación académica de **5/5**: no se recibió la rúbrica del profesor. El puntaje 1/5 comunicado por el equipo no aparece en los archivos consultados. Los hallazgos se clasifican por evidencia técnica, no por una equivalencia inventada con esa calificación.

Referencias de código revisadas antes de aplicar las correcciones de esta misión:

| Repositorio | Rama de origen | Commit base revisado |
|---|---|---|
| Backend | `release/v1.0.0-rc1` | `bed0739c887a239c81a37311f076c3c315ace9bf` |
| Frontend | `release/v1.0.0-rc1` | `44122eb1f83dd720995b60b2b1deb6f9011aa89c` |
| Database | `release/v1.0.0-rc1` | `b8fbcdbf3edee9ef733818517b19fe0b37ba35d4` |
| Context | `docs/mvp-context-consolidation` | `651aa7b9dfd15e0b03f4540f4821f3ba93ab9a7f` |

Los cambios de esta misión están en preparación local; esta tabla no afirma que ya estén publicados en `main`.

## Controles observados

- **Contraseñas y sesión:** Argon2id, JWT con algoritmo/issuer/audience/tipos de claims validados, expiración limitada y token del navegador solo en memoria. Las respuestas API se marcan `no-store`; el frontend usa `cache: no-store`, `credentials: omit` y rechaza redirecciones.
- **Autorización:** la API aplica propiedad individual en V1. V2 añade roles de workspace, firma HMAC de contexto por transacción y políticas RLS; el cliente no suministra la identidad firmada. El rol SQL de la API se comprueba para que no sea superusuario, creador de roles, creador de bases o bypass de RLS.
- **SQL y entradas:** SQLAlchemy y SQL explícito parametrizado, validación Pydantic, campos extra rechazados en contratos, límites de cuerpos/paginación y errores sin incluir los datos recibidos. El ORM no reemplaza controles de autorización ni la revisión de SQL manual.
- **NVD:** endpoint fijo, TLS verificado, resolución y pin de IP por conexión, sin proxies heredados del entorno ni redirecciones, parámetros acotados, presupuestos de tiempo/tamaño/páginas, cuota, caché y conservación de cobertura/procedencia. Las coincidencias se tratan como potenciales; no prueban explotación.
- **Catálogo y base:** lector CPE separado de la base privada y de solo lectura; grants de mínimo privilegio, RLS por workspace en V2 y funciones `SECURITY DEFINER` con `search_path` restringido y permisos revocados a `PUBLIC`. Algunas tablas de verificación, dispositivos y catálogo privado usan además `FORCE ROW LEVEL SECURITY`.
- **Contenedores de producción:** digests fijados, procesos de aplicación/proxy sin root, filesystem de solo lectura, capacidades eliminadas, `no-new-privileges`, bases en redes privadas con TLS y puertos publicados solo en loopback por defecto. El proxy aplica CSP, HSTS, `nosniff`, protección contra framing y límites HTTP.
- **Frontend:** no se encontró uso de `dangerouslySetInnerHTML`, `eval`, `localStorage` o `sessionStorage`; los enlaces externos usan `noopener noreferrer` y se validan cuando el origen viene de datos.

## Correcciones aplicadas en esta misión

1. El proxy aumenta HSTS de un día a un año (`max-age=31536000`), sin `includeSubDomains`, porque el dominio y sus subdominios no están bajo esta revisión.
2. El modo NVD de la imagen web y de Compose de producción queda `disabled` por defecto, coherente con la configuración backend de producción. Para cambiarlo se requiere habilitar explícitamente el backend y reconstruir la interfaz; `VITE_NVD_MODE` no se considera autorización.
3. Se añadió una vista previa de la interfaz y un logotipo PNG transparente en la raíz de cada repositorio. La vista previa usa una captura existente de la interfaz y datos sintéticos, no evidencia de una consulta NVD en vivo.
4. Se amplió el README del Backend con arquitectura, flujo NVD/CPE, endpoints, controles, limitaciones y operación.

## Hallazgos pendientes por severidad

### Alto — Salida de red del API sin filtro por destino a nivel de infraestructura

**Evidencia:** `vuln-track-backend/deploy/production/compose.yaml` conecta el API a una red Docker `egress` ordinaria. La aplicación actual restringe los clientes NVD a hosts y rutas fijos, pero la red no impone una lista permitida de destinos si se compromete el proceso o se incorpora código futuro.

**Impacto:** un proceso comprometido podría iniciar conexiones salientes no relacionadas con NVD y facilitar exfiltración o movimiento hacia servicios externos. Esto no demuestra una SSRF explotable por una entrada actual del usuario; es una brecha de defensa en profundidad del despliegue.

**Estado:** no corregido. Una política segura requiere un proxy/gateway o firewall de salida de la plataforma destino, probados con IPv4/IPv6 y fallo cerrado. Convertir ahora la red en `internal` rompería consultas NVD live; no se cambió el comportamiento sin un reemplazo controlado. No habilitar salida live en producción hasta aprobar y comprobar ese límite en el host.

### Alto — Avisos conocidos en las imágenes fijadas

**Evidencia:** `vuln-track-backend/deploy/production/SEGURIDAD_IMAGENES.md` registra un análisis local de 24–25 de noviembre de 2026: 1 CRITICAL y 45 HIGH en PostgreSQL, 53 HIGH en Backend y 2 HIGH en Caddy. El informe considera que el CRITICAL de `gosu` no es alcanzable en la función observada, pero lo conserva para reevaluación; no declara las imágenes libres de vulnerabilidades.

**Impacto:** los avisos deben reevaluarse contra las imágenes y rutas alcanzables del despliegue final. La evaluación documentada es fechada y no representa un escaneo actualizado al 27 de noviembre.

**Estado:** no corregido. No se sustituyeron tags/digests sin identificar primero una imagen oficial parcheada y validar compatibilidad. Antes de publicar, actualizar imágenes de forma revisada, volver a escanear los digests exactos y conservar el reporte resultante.

### Medio — Registro público y abuso de cuentas

**Evidencia:** la herramienta de despliegue genera `registration_enabled=true`; el control tiene límites de frecuencia locales al proceso y permite alta sin correo/verificación. El flujo crea un workspace inicial aislado por cuenta.

**Impacto:** un actor distribuido puede automatizar altas; los contadores por proceso se reinician al reiniciar el backend. La limitación global actual no sustituye una cuota duradera ni prueba de identidad.

**Estado:** permanece habilitado según la solicitud del usuario de permitir crear cuentas. Para exposición pública o crecimiento, se requiere decidir entre invitaciones, verificación externa o controles de abuso con almacenamiento compartido y monitoreo. No se agregó un proveedor CAPTCHA/correo sin aprobación de arquitectura y datos.

### Medio — JWT sin revocación individual

**Evidencia:** el token de producción tiene vigencia corta (300 s), pero no hay refresh ni endpoint de revocación individual. Logout elimina el token en el navegador; no invalida una copia previamente robada.

**Impacto:** una sesión comprometida puede usarse hasta que expire o se rote el secreto/cambie la versión del usuario.

**Estado:** mitigado parcialmente por el TTL corto, no resuelto. La corrección completa requiere un diseño de sesión revocable, persistencia, limpieza, auditoría y pruebas de fallo cerrado. No se amplió la vigencia ni se añadió almacenamiento local.

### Medio — Limitadores locales y escalamiento

**Evidencia:** límites de login/registro y cuota de algunas operaciones son en memoria por proceso. La configuración de producción usa un worker y una réplica.

**Impacto:** reinicios limpian el estado y varias réplicas no comparten cuotas; escalar sin servicio compartido debilita los límites.

**Estado:** válido para la topología documentada de una réplica; antes de escalar se debe migrar la cuota a almacenamiento compartido o al perímetro y comprobar concurrencia.

### Medio — Aislamiento V1 depende de la API

**Evidencia:** las rutas heredadas de activos filtran por `created_by` en la API, mientras que la separación RLS/HMAC corresponde a V2. La base V1 no tiene una política de workspace que restrinja esas filas por usuario.

**Impacto:** una consulta ejecutada con credenciales SQL V1 fuera de las rutas controladas de la API puede leer más de un propietario. No hay conexión SQL desde el frontend; las credenciales deben permanecer privadas y nunca usarse en clientes móviles/web.

**Estado:** no se cambió el esquema. La instalación de producción documentada usa permisos restringidos y RLS para recursos V2; si se conserva V1 para operaciones multiusuario, diseñar y probar un contexto de usuario/RLS equivalente antes de ampliar su exposición.

### Medio — Respaldo externo, host y operación

**Evidencia:** la validación local no es una aceptación de EC2/host. La guía pide cifrar y restringir copias externas, comprobar firewall/certificado/actualizaciones/observabilidad y ensayar recuperación en el servidor destino.

**Impacto:** una falla del host, pérdida de secretos/backups o imagen vulnerable puede comprometer confidencialidad y disponibilidad aunque el código tenga controles.

**Estado:** pendiente de operación; el candidato sigue **NO-GO público** hasta verificar los controles del host y recuperación externa.

### Alcance y coherencia documental — requiere coordinación

El contexto de aprobación conserva pendientes la ratificación técnica global y la integración de workspaces V2, mientras que las ramas RC actuales contienen esas funciones. Esto no es por sí mismo una vulnerabilidad, pero impide tratar la publicación en `main` como prueba de aprobación técnica. Conservar los estados `PENDIENTE` y coordinar la decisión entre responsables antes de presentar el alcance como aceptado.

## Cierre

Se corrigieron dos valores predeterminados de seguridad y se documentó el resto con evidencia. Quedan hallazgos de infraestructura, imágenes, abuso de registro y revocación que impiden afirmar “muy seguro” en sentido absoluto o una nota de 5/5 sin rúbrica. El resultado actual es un **candidato con controles relevantes y riesgos explícitos**, no una certificación ni autorización de despliegue público.
