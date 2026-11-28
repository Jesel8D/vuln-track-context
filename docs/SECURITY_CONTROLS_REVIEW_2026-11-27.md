# Revisión de controles de seguridad — 27 de noviembre de 2026

## Alcance y límite de esta revisión

Revisión estática y pruebas unitarias locales de Backend y Frontend. Se inspeccionaron autenticación, autorización, validación, consultas SQL, integración NVD, configuración de red y Docker, almacenamiento de sesión y límites operativos. No se inició el stack ni se atacaron servicios públicos o activos registrados. La instancia Docker no estaba disponible. Sí se verificaron controles de request/SSRF/NVD con pruebas locales, se consultó una vez el API oficial NVD en modo de solo lectura y se auditaron los locks Python/Node.

**Pruebas locales de la revisión previa:** Backend: 260 pruebas relevantes pasaron; se excluyeron 5 pruebas de laboratorio incompatibles con ese Windows/TLS. Frontend: `npm run check` terminó correctamente con 181 pruebas, typecheck, ESLint y build. `npm audit` no reportó avisos en el árbol completo del Frontend; `pip-audit` no encontró vulnerabilidades conocidas en los lockfiles de Backend. Una consulta GET al NVD oficial con un CPE público de prueba devolvió `CVE-2017-6784` como resultado real. Estos resultados no equivalen a un pentest. En la revisión local posterior, pasó una prueba aislada de lock y `py_compile`; la suite backend completa no pudo recolectarse porque el Python global carece de `argon2-cffi`. Esta diferencia de entornos queda registrada; no se afirma que la suite completa haya pasado en la revisión posterior.

No se hizo DoS/DDoS ni una prueba de carga contra Internet, la instancia desplegada o un servicio compartido. No se enviaron payloads SQL a PostgreSQL ni payloads XSS a una instancia de la app: el servicio DB/app no estaba levantado. Docker Scout está instalado, pero el escaneo de imágenes requiere iniciar sesión con Docker ID y no se usaron credenciales; además, el daemon Docker no estaba activo para construir y escanear las imágenes finales.

Esta revisión no sustituye una auditoría independiente ni permite afirmar una calificación académica de **5/5**: no se recibió la rúbrica del profesor. El puntaje 1/5 comunicado por el equipo no aparece en los archivos consultados. Los hallazgos se clasifican por evidencia técnica, no por una equivalencia inventada con esa calificación.

Referencias de código revisadas antes de aplicar las correcciones de esta misión:

| Repositorio | Rama de origen | Commit base revisado |
|---|---|---|
| Backend | `release/v1.0.0-rc1` | `bed0739c887a239c81a37311f076c3c315ace9bf` |
| Frontend | `release/v1.0.0-rc1` | `44122eb1f83dd720995b60b2b1deb6f9011aa89c` |
| Database | `release/v1.0.0-rc1` | `b8fbcdbf3edee9ef733818517b19fe0b37ba35d4` |
| Context | `docs/mvp-context-consolidation` | `651aa7b9dfd15e0b03f4540f4821f3ba93ab9a7f` |

La tabla conserva las bases analizadas en la primera revisión. Los commits iniciales de esta misión se publicaron en `main`; las correcciones adicionales enumeradas en esta actualización también se publican en esa rama.

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
2. **No se modificaron las banderas NVD/CVE en esta revisión ampliada.** La ruta de consulta real permanece disponible y se verificó con una solicitud al servicio oficial. La plantilla para instalaciones nuevas conserva la configuración existente con `NVD_MODE=disabled` y `CVE_LIVE_ENABLED=false`; para usar el botón real, el operador debe habilitar el backend con `operate.py configure --cve-live on` y compilar/recrear el frontend con `VITE_NVD_MODE=live`. La bandera del frontend no habilita por sí misma el acceso del servidor.
3. Se añadió una vista previa de la interfaz y un logotipo PNG transparente en la raíz de cada repositorio. La vista previa usa una captura existente de la interfaz y datos sintéticos, no evidencia de una consulta NVD en vivo.
4. Se amplió el README del Backend con arquitectura, flujo NVD/CPE, endpoints, controles, limitaciones y operación.
5. Se corrigió una incompatibilidad real de Windows: `core.autocrlf=true` convertía a CRLF varios esquemas NVD cuyo hash se comprueba byte por byte, provocando que se rechazaran todas las respuestas CVE. `.gitattributes` fija LF para los esquemas JSON; se verificaron todos los hashes y pasó el conjunto de pruebas NVD.
6. El middleware de request ahora rechaza un `Content-Length` que supera el máximo antes de leer el cuerpo y compara cada fragmento antes de copiarlo al buffer. Se añadió una prueba que confirma la respuesta 413 sin leer el cuerpo.
7. El Compose de producción ahora limita procesos a PostgreSQL/Catálogo (256), API (128) y Proxy (64), además de los límites de memoria y CPU ya existentes.
8. Se actualizaron las imágenes oficiales fijadas de PostgreSQL a `16.16-alpine3.24` y de Node de compilación a `24.22.3-alpine3.24`, conservando digests inmutables. Los manifiestos se consultaron en el registro oficial; no se construyeron ni escanearon las imágenes por falta del daemon/credenciales de Scout.
9. Se corrigió el anuncio accesible del error de confirmación de contraseña y los selectores de sus pruebas unitarias.

## Hallazgos pendientes por severidad

### Alto — Salida de red del API sin filtro por destino a nivel de infraestructura

**Evidencia:** `vuln-track-backend/deploy/production/compose.yaml` conecta el API a una red Docker `egress` ordinaria. La aplicación actual restringe los clientes NVD a hosts y rutas fijos, pero la red no impone una lista permitida de destinos si se compromete el proceso o se incorpora código futuro.

**Impacto:** un proceso comprometido podría iniciar conexiones salientes no relacionadas con NVD y facilitar exfiltración o movimiento hacia servicios externos. Esto no demuestra una SSRF explotable por una entrada actual del usuario; es una brecha de defensa en profundidad del despliegue.

**Estado:** no corregido. Una política segura requiere un proxy/gateway o firewall de salida de la plataforma destino, probados con IPv4/IPv6 y fallo cerrado. Convertir ahora la red en `internal` rompería consultas NVD live; no se cambió el comportamiento sin un reemplazo controlado. No habilitar salida live en producción hasta aprobar y comprobar ese límite en el host.

### Alto — Avisos conocidos en las imágenes fijadas

**Evidencia:** `vuln-track-backend/deploy/production/SEGURIDAD_IMAGENES.md` registra un análisis local de 24–25 de noviembre de 2026: 1 CRITICAL y 45 HIGH en PostgreSQL, 53 HIGH en Backend y 2 HIGH en Caddy. El informe considera que el CRITICAL de `gosu` no es alcanzable en la función observada, pero lo conserva para reevaluación. PostgreSQL y Node ahora apuntan a tags/digests oficiales más recientes revisados el 27-11-2026; las imágenes finales todavía no se volvieron a escanear. Python y Caddy mantienen sus digests fijados.

**Impacto:** los avisos deben reevaluarse contra las imágenes y rutas alcanzables del despliegue final. La evaluación documentada es fechada y no representa un escaneo actualizado al 27 de noviembre.

**Estado:** mitigación parcial. Se actualizaron PostgreSQL y Node a versiones oficiales más recientes con digest fijado. No puedo asegurar que los avisos anteriores hayan desaparecido ni que los digests nuevos estén limpios sin construir y escanear las imágenes exactas. Ejecutar Docker Scout/Trivy después de reactivar Docker y conservar el reporte actualizado antes del despliegue.

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

Se corrigieron el rechazo temprano de cuerpos sobredimensionados, el límite de procesos de contenedores y el bloqueo de respuestas NVD por finales de línea en Windows; también se actualizaron dos imágenes base. Pasaron las pruebas locales indicadas y los audits de dependencias no encontraron vulnerabilidades conocidas. Siguen pendientes el filtro de egress, el escaneo de imágenes finales, un test de penetración autorizado en un entorno aislado, control compartido de abuso, revocación JWT, infraestructura/backups y decisión de operación de NVD live. No se hizo DDoS ni pruebas contra activos desplegados. El resultado es un **candidato con controles relevantes y brechas documentadas**, no una certificación ni autorización de despliegue público.

## Actualización local — launcher Windows

En una revisión posterior del 2026-11-27 se encontraron usos POSIX de `fcntl` y `killpg` en el launcher local documentado para PowerShell. Se añadieron locks no bloqueantes con `msvcrt` en Windows, comprobación de creación de proceso con Windows API y terminación de árbol de procesos con `taskkill /T`; la ruta POSIX conserva `flock`/`killpg`. La prueba aislada del lock pasó y los módulos compilaron con `py_compile`. La suite completa no se pudo recolectar en este entorno porque falta `argon2-cffi` en Python global; no se inició el stack. `scripts/nvd_trial_lab.py` permanece intencionalmente POSIX-only por su ledger de autorizaciones con fsync de directorio.
