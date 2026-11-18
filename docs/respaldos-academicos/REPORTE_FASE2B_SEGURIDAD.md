> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 — entrega de Fase 2B

Fecha: 15 de noviembre de 2026. Cierre de protección comprobado a las 22:49 UTC
(16:49 de Ciudad de México).

**Resultado: IMPLEMENTADO CON LIMITACIONES.** Hay desafíos duraderos, RLS real,
API y React integrados, DNS acotado y transporte reutilizado con pruebas de red
aisladas. **El egreso público selectivo sigue PENDIENTE; toda verificación remota
operativa y ZAP permanecen BLOQUEADOS.** Una coincidencia de laboratorio nunca
acredita control real del dominio. La instalación personal no recibió esta fase.

## A. Estado inicial — IMPLEMENTADO Y PROBADO

Se inspeccionaron los cuatro repositorios, sus ramas, worktrees, contenedores,
volúmenes y puertos; además, autenticación, membresías, destinos/declaraciones,
SSRF, transporte, desafíos, auditoría, RLS, worker, React, OpenAPI y Postman.
Las bases publicadas de Fase 2A se verificaron antes de trabajar:

| Repositorio | Base de esta fase |
|---|---|
| Backend | `7198de7c986f6b86a0f2ac1a94d1d88b893b3a84` |
| Frontend | `1a0263a859ecdbf844887d27ae17c7c6fa903ed7` |
| Database | `b377b5bd8013cc2151f6a1681d168e1df0f5fe42` |
| Context, sin cambios | `caa7ef13b0e2b24848991e32b3c0ee3fc59139c7` |

Faltaba integrar persistencia y RLS de desafíos, DNS acotado, servicio y estados
duraderos de interfaz. Se reutilizaron política SSRF y transporte de Fase 2A;
solo se extrajo la generación compartida de tokens para evitar duplicarla.

La instalación personal de `/home/USUARIO/VulnTrack` continúa limpia en
Backend `fcabbe986f2a4d3dffb259edda0b7245e533ee91`,
Database `b377b5bd8013cc2151f6a1681d168e1df0f5fe42`,
Frontend `4514b6840b428152c1cd875c95e3e55ad81c4196` y el Context anterior.
El volumen protegido `vulntrack_v2_development_pgdata` sigue montado en su
contenedor saludable, publicado solo en `127.0.0.1:55434`. Los procesos originales
siguen trabajando desde esos checkouts. `/health`, `/ready`, `/docs` en 8000 y la
web en 5173 devolvieron HTTP 200 al cierre. No se ejecutaron migraciones, pruebas
adversariales, restauraciones ni cambios de cuentas sobre esa base.

## B. Ramas y commits — IMPLEMENTADO Y PROBADO

Worktrees independientes en `/home/USUARIO/VulnTrack-security-phase2b`:

| Repositorio | Rama publicada | Commit exacto |
|---|---|---|
| Backend | `feat/v2-security-analysis-phase2b` | [7a158b895e750bef8b2b580339a43f863da046ac](https://github.com/Jesel8D/vuln-track-backend/commit/7a158b895e750bef8b2b580339a43f863da046ac) |
| Database | `feat/v2-security-verification-database` | [a562e638dfe5b7e8d07eec0e8046e6998f8a2802](https://github.com/Jesel8D/vuln-track-database/commit/a562e638dfe5b7e8d07eec0e8046e6998f8a2802) |
| Frontend | `feat/v2-security-analysis-web-phase2b` | [0ab0f117651dd058cb2cb70d994999781e18e75f](https://github.com/Jesel8D/vuln-track-frontend/commit/0ab0f117651dd058cb2cb70d994999781e18e75f) |

Los tres pushes fueron normales y `ls-remote` coincidió con cada HEAD completo.
Los worktrees quedaron limpios. El manifiesto de Backend referencia el commit
exacto de Database y comprueba los hashes SQL. Se revisaron archivos preparados
contra valores privados de la configuración personal y claves privadas: sin
coincidencias. No se publicaron dumps, `.local`, artefactos privados ni secretos.
No hubo merges, force push ni cambios a main/rudi/MVP-B.

## C. Backend — IMPLEMENTADO CON LIMITACIONES

Se agregó una capa de servicio con identidad JWT, membresía vigente, contexto
HMAC transaccional, recuperación del recurso exacto y operaciones SQL auditadas.
Cinco operaciones nuevas bajo
`/api/v2/workspaces/{workspace_id}/applications/{application_id}/targets/{target_id}/verifications`:

| Operación | Resultado |
|---|---|
| GET colección | Metadatos reales, máximo 20 registros |
| POST colección | Emisión 201; repetición idempotente 200 sin reproducir token |
| GET `/{challenge_id}` | Recurso sujeto a scope y RLS |
| POST `/{challenge_id}/revoke` | Revocación versionada e idempotente |
| POST `/{challenge_id}/verify` | 503 para un desafío válido pendiente; sin DNS, red, cola ni intento |

Los cuerpos y respuestas tienen esquemas Pydantic. Se preservan las 39
operaciones anteriores; OpenAPI contiene 44 operaciones. Se documentan errores
de autenticación, recurso, versiones, cuotas y validación. La emisión entrega
instrucciones una sola vez con `Cache-Control: no-store`. No se hacen solicitudes
externas al registrar un destino o emitir un desafío. El flujo operativo de
verificación permanece incompleto deliberadamente por falta de egreso demostrado.
Véase el contrato HTTP (referencia local no incluida).

## D. Database — IMPLEMENTADO Y PROBADO

Database administra `sql/008_v2_target_verification.sql`; Backend conserva ORM y
una línea Alembic explícita independiente, conforme D-002. SQL008 agrega
`vulntrack_verification.challenges` y `attempts`, constraints, índices, roles,
políticas, triggers y funciones acotadas. Preserva las estructuras y 29 políticas
legadas; añade grants mínimos al nuevo propietario técnico.

La línea anterior conserva `public.alembic_version=0004_mvp_cve`; la nueva usa
`public.alembic_verification_version`. Se probaron migración desde SQL007,
bootstrap limpio y adopción únicamente tras comparar el catálogo exacto. El
verificador rechaza diferencias de columnas, constraints, RLS, ACL, roles,
propietarios, funciones, search_path, triggers e índices. No hay stamp ciego.
El downgrade destructivo se rechaza para conservar evidencia.
Véase el modelo SQL y roles (referencia local no incluida).

## E. Frontend — IMPLEMENTADO Y PROBADO

React consulta metadatos reales y permite emitir/revocar según el rol. Presenta
estados persistidos y efectivos, caducidad, revocación, errores y disponibilidad.
Valida contrato e IDs de workspace/aplicación/destino. Las instrucciones viven
solo en memoria del componente y se eliminan al revocar o cambiar de destino;
no se guardan en localStorage ni cachés de metadatos. Una respuesta perdida puede
reintentarse con la misma clave idempotente sin revelar nuevamente el token.

Una evidencia de laboratorio se etiqueta explícitamente. No se fabrica un
resultado completado ni una fecha de verificación. No se ofrece una operación
remota disponible. Baseline sigue deshabilitado. Se comprobó accesibilidad Axe,
ausencia de overflow y comportamiento a 1440 y 390 píxeles.
Véase la integración React (referencia local no incluida).

## F. Evidencia persistente — IMPLEMENTADO CON LIMITACIONES

Se conservan UUID y relaciones completas del destino, workspace, aplicación y
actor; propósito/método; origen HTTPS exacto; digest de URL; versión/vigencia de
declaración; SHA-256 del cuerpo esperado; estados, versiones, intentos y fechas.
No se guardan tokens en claro, cuerpos HTTP completos ni credenciales del sitio.
La URL completa no se duplica en la evidencia; se usa su digest.

Los intentos tienen procedencia restringida a `laboratory`, lease, resultado y
metadatos privados. Incluso una coincidencia termina como
`failed/LABORATORY_MATCH_ONLY`, `verified_at=null`, `control_verified=false`.
La transición a `verified` está bloqueada en esta entrega. Habilitar evidencia
operativa requerirá un productor confiable y una migración explícita posterior.

La prueba persistente conservó IDs, estados, intentos y fechas tras reinicio
completo y backup/restore en OTRO volumen nuevo. Comprobó lectura y verificadores
del código estable y de Fase 2A, RLS forzado y comportamiento JWT. Los volúmenes
finales quedan retenidos y sus contenedores detenidos:

- `vulntrack_v2_verification_test_8c34435fb5_pgdata`
- `vulntrack_v2_verification_test_restore_8c34435fb5_pgdata`

Véase la evidencia resumida (referencia local no incluida).
La prueba usó datos sintéticos propios; no acredita un dominio real.

## G. RLS — IMPLEMENTADO Y PROBADO

Ambas tablas tienen ENABLE y FORCE ROW LEVEL SECURITY, seis políticas y FK
compuestas. Lectura: owner/editor/viewer. Escritura: owner/editor mediante
funciones, sin DML directo del rol API. Se probaron usuarios/workspaces ajenos,
firmas inválidas, IDs mezclados, permisos, conexiones reutilizadas y rollback
con PostgreSQL real.

`vulntrack_app` lee solo metadatos y ejecuta issue/revoke; no puede leer digests
privados ni ejecutar funciones de laboratorio. `vulntrack_registration` no recibe
USAGE ni permisos en el esquema nuevo. El rol lab es NOLOGIN/NOBYPASSRLS y no se
concede al login API. El worker de red no recibe credenciales SQL/API/HMAC.

Las cinco funciones SECURITY DEFINER tienen propietario
`vulntrack_verification_owner`, NOLOGIN/NOSUPERUSER/NOBYPASSRLS, sin miembros,
search_path fijo `pg_catalog` y nombres calificados. Solo tiene los permisos
mínimos de referencias, lectura, bloqueo de filas y auditoría; no lee claves de
contexto. PUBLIC no recibe EXECUTE. El administrador del host/DB permanece como
autoridad de confianza; estas medidas no lo aíslan.

## H. Autorización — IMPLEMENTADO Y PROBADO

Se distinguen declaración de propiedad, control técnico y elegibilidad de
ejecución. Una declaración aprobada no activa ninguna de las dos últimas.
El recurso queda vinculado al workspace/destino exactos, esquema, host y puerto;
no se extiende a subdominios o URLs distintas. Cambiar URL, declaración, vigencia,+membresía o estado de aplicación invalida su uso. Se vuelve a comprobar el scope
al consumir y finalizar.

Locks SQL, versiones, unicidad y transacciones protegen emisión, consumo único,
idempotencia, revocación, expiración y contención. Se comprobaron rollback de
auditoría e intentos repetidos. Límites propuestos: TTL de 10 minutos, un intento,
lease de 15 segundos, un pending por destino y 20 emisiones/workspace/24 horas.
Requieren ratificación de equipo. La lectura deriva caducidad sin inventar
fechas ni reescribir el historial. El contexto HMAC heredado no se presenta como
nonce de uso único; el consumo único lo garantiza el desafío transaccional.

## I. DNS — IMPLEMENTADO CON LIMITACIONES

El resolutor controlado usa dnspython 2.9.2 y un endpoint numérico TCP/53 elegido
por operador. No acepta resolutores del cliente ni usa resolución silenciosa
del sistema. Normaliza IDNA, revisa A/AAAA, CNAME y direcciones adicionales;
una dirección prohibida rechaza todo el conjunto. Rechaza loops, ambigüedades,+alcance ajeno, profundidad excesiva y cambios entre familias/resoluciones.

Límites: 2 segundos/resolución, 4096 bytes antes de leer el cuerpo, 16 IP, 32
RRsets, 4 CNAME y 10 consultas; sin caché, reintentos o fallback UDP. Dos
resoluciones frescas comparan cadena e IP antes del transporte fijado. Se probó
con DNS TCP real IPv4/IPv6 dentro del laboratorio. DNS operativo autorizado,+su canal y administración siguen PENDIENTES; no se afirma DNSSEC ni despliegue.

## J. Transporte SSRF — IMPLEMENTADO CON LIMITACIONES

Se reutiliza HTTPS de Fase 2A con conexión numérica fijada, Host/SNI originales y
validación TLS/certificado. Sin proxies del entorno, redirecciones, credenciales
en URL, reintentos ni bypass de certificados. Conserva límites de cabeceras,
cuerpo, I/O y cancelación; el conjunto DNS/HTTPS tiene deadline de 10 segundos.

Las pruebas de red ejercieron IPv4/IPv6, SNI/Host, certificados incorrectos,
expirados/no confiables, respuestas excesivas, redirects, timeouts y cierre de
sockets. El adaptador compuesto no está instanciado por la API operativa.
Su seguridad con egreso público continúa pendiente de una plataforma probada.

## K. Egreso de red — PENDIENTE

**Pendiente para Internet; implementado y probado exclusivamente en laboratorio.**
El fixture y el worker comparten un namespace `network none`, sin rutas externas.
Un helper efímero NET_ADMIN aplica OUTPUT DROP IPv4/IPv6 dentro de ese namespace,
permitiendo DNS TCP solo al resolutor de prueba y HTTPS solo a IP autorizadas.
No se modificaron reglas de Windows, WSL host ni Docker Desktop.

El worker usa UID 65532, capacidades cero, no-new-privileges, raíz read-only,
CPU/memoria/PIDs/FD/logs limitados, sin host networking, privileged, docker.sock,
archivos del usuario o secretos de aplicación. El broker SQL de prueba está
fuera del worker y usa PostgreSQL desechable.

Se saltó deliberadamente la política Python: canarios escuchando de DB, privados,
host/metadatos, HTTP y DNS no permitido recibieron cero conexiones/datagramas.
También se bloqueó el alias TLS devuelto tras rebinding. Se probaron límites
cgroup y OOM en una sonda propia de 32 MiB. Los alias con apariencia pública son
locales al namespace y no son servicios de terceros.

Esto no demuestra filtrado selectivo hacia Internet ni una barrera en una red
Docker ordinaria. El host/daemon y el helper son autoridades de confianza.
Mientras esa política pública no se demuestre, la API sigue devolviendo 503.
Véanse el laboratorio (referencia local no incluida)
y sus 45 resultados (referencia local no incluida).

## L. Pruebas — IMPLEMENTADO Y PROBADO

Estos resultados se ejecutaron en esta misión; no se reutilizaron conteos de
Fase 2A como evidencia nueva:

| Recorrido | Resultado observado |
|---|---|
| Backend, instalación existente compatible | 677 passed, 0 fallos, 0 skipped; 158.84 s |
| Backend, instalación nueva | 677 passed, 0 fallos, 0 skipped; 158.63 s |
| SQL por recorrido | 120 comprobaciones: 96 previas repetidas + 24 nuevas |
| SQL adversarial separado | 27 comprobaciones, PostgreSQL tmpfs propio |
| React | 96 pruebas, 8 archivos; 86 anteriores + 10 nuevas |
| Navegador, existente | 6 passed; 19.7 s |
| Navegador, nueva | 6 passed; 16.3 s |
| Red DNS/TLS/aislamiento/recursos | 45 checks: 27 anteriores repetidos + 18 adicionales |
| Postman, existente | 298 aserciones, 77 envíos HTTP, 0 fallos |
| Postman, nueva | 293 aserciones, 76 envíos HTTP, 0 fallos |
| Postman, contrato persistente desechable | 200 aserciones, 53 envíos HTTP, 0 fallos |
| Reinicio, backup/restore y compatibilidad antigua | Passed, en perfiles/volúmenes NUEVOS |
| Ruff, pip check, TypeScript, ESLint y build | Sin fallos |
| Git diff --check y escaneo previo a publicación | Sin fallos |

De los 677 casos Backend, **79 son nuevos**: 39 DNS unitarios, 39 de integración
(32 de ciclo/RLS y 7 de rechazo de drift) y uno del contrato HTTP tipado. Los
598 anteriores están incluidos en esos 677. Los dos recorridos repiten el
conjunto; no representan 1354 escenarios distintos. Navegador conserva seis
casos ampliados con el nuevo flujo, sin contarlos como seis casos nuevos.
Los envíos Postman varían por polling; no se suman como pruebas únicas. La
colección define 71 solicitudes y el validador revisó 23 cuerpos Pydantic y 49
ejemplos positivos. Solo se añadió el pin DNS indicado; no se actualizaron los
demás pins ni los locks npm. Persisten advertencias heredadas de TestClient y
Newman sin fallos asociados.

Logs privados, ignorados por Git:
`/home/USUARIO/VulnTrack-security-phase2b/vuln-track-backend/api-tests/v2/.artifacts/phase2b/`.
Los resúmenes sanitizados entregados están en
[fase2b-seguridad/evidencia](fase2b-seguridad/evidencia/RESULTADOS.md).

## M. Regresiones — IMPLEMENTADO Y PROBADO

Los recorridos completos ejercieron registro/login, JWT, workspaces/membresías,
aplicaciones, tecnologías, CPE/CVE, recomendaciones, dashboard, API existente y
Postman, con NVD fixture. El contrato base de las 39 operaciones anteriores se
conservó. Los ejemplos nuevos son sintéticos, sin tokens de cuentas.

Se comprobó compatibilidad de lectura del código estable y Fase 2A con SQL008
presente, incluidos sus verificadores estrictos de catálogo. No se aplicaron
cambios experimentales a la instalación personal. Los backups restaurados se
ensayaron en otro volumen y los contenedores de pruebas persistentes quedaron
detenidos; se conservaron sus volúmenes.

## N. Estado ZAP — BLOQUEADO

ZAP no fue instalado, ejecutado ni integrado. Baseline continúa deshabilitado;
no hay endpoint de ejecución, botón oculto o flag que lo active. No se realizaron
escaneos externos ni consultas NVD live. La publicación de ramas y las pruebas
no constituyen una aprobación operativa. El
checklist actualizado (referencia local no incluida)
separa controles implementados, pruebas, pendientes y decisiones del equipo.

## O. Limitaciones — IMPLEMENTADO CON LIMITACIONES

- No existe verificador remoto operativo ni productor de evidencia real; todo
  resultado de laboratorio permanece separado y nunca produce `verified`.
- Falta demostrar DNS/egreso selectivo en una plataforma con rutas públicas.
  WSL2/network-none no certifica producción ni aisla del administrador del daemon.
- No hay scheduler duradero, recuperación/reconciliación de jobs, cancelación
  operativa, cuotas de ejecución o gestión de evidencia operativa.
- Un intento lab abandonado requiere revocación o expiración; no existe un
  reconciliador que cierre automáticamente ese intento.
- Retención, renovación, vigencia y límites actuales son propuestas pendientes
  de ratificación, no decisiones globales aprobadas.
- El transporte mantiene la restricción a HTTPS y el método actual; no amplía
  autorización a otros puertos, subdominios o métodos.
- Las imágenes base y pins Python están fijados; los paquetes apt del laboratorio
  no tienen snapshot binario. El ID de imagen ensayada está en su JSON de evidencia.
- No se aprobó una nueva arquitectura conjunta ni se fusionaron líneas MVP-B.

## P. Requisitos para Fase 3 — PENDIENTE

1. Acordar con Jesel/Rudi alcance/método de control y autorización operativa por
   destino, responsables, vigencia, renovación, retención, cuotas y revisión.
2. Elegir y desplegar DNS autorizado y egreso por IP/puerto con denegación por
   defecto independiente de Python; probar rutas públicas, IPv4/IPv6, rebinding,
   cambios dinámicos, acceso al host/DB/privados/metadatos y protocolos prohibidos.
3. Implementar un productor confiable de evidencia operativa, con separación del
   cliente/laboratorio y migración explícita que permita estados verificados.
4. Diseñar runner por job sin secretos ni acceso a aplicación/DB/host/Docker,
   scheduler, presupuestos, concurrencia, cancelación/revocación y recuperación.
5. Ejecutar revisión adversarial integrada, backups/restores y observabilidad
   sanitizada en esa plataforma, con ensayos de fallo y recuperación.
6. Decidir explícitamente una futura fase ZAP Baseline y el alcance autorizado de
   cada ejecución. Completar pruebas o desplegar esta fase no concede ese permiso.

## Q. Actualización de la instalación personal — PENDIENTE

Está preparado el [procedimiento con hashes exactos y reversión](fase2b-seguridad/backend/docs/V2_PHASE2B_LOCAL_UPGRADE.md).
**No se ejecutó sobre la instalación personal.** Incluye:

1. Vista previa opcional independiente con perfil nuevo y puertos separados;
   comprobar colisiones antes de iniciarla.
2. Ventana decidida por el usuario/equipo, revisión de estados, backup nuevo y
   restauración de ensayo verificada antes de migrar.
3. Actualizar juntos Backend, Database y Frontend a los tres commits publicados;
   instalar dependencias bloqueadas y ejecutar explícitamente la línea Alembic
   de verificación. El launcher no migra un perfil existente automáticamente.
4. Comprobar identidad, datos, historial, RLS, metadatos, salud y bloqueo remoto.
5. Ante problemas, volver al código original y dependencias con el mismo perfil,
   conservando SQL008 y sus evidencias. No hay downgrade destructivo ni restore
   encima de la base personal.

El backup personal histórico de Fase 1 permanece privado; esta fase no lo
restauró. Para una actualización futura se necesita uno nuevo y probado. Las
copias de documentación en esta entrega no contienen claves ni DSN privados.

Documentación adicional: arquitectura, reproducción y límites (referencia local no incluida).
Metadatos de entrega: .setup/security-phase2b-delivery.json (referencia local no incluida).
