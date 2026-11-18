> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 — entrega de Fase 2C

Fecha local: 15 de noviembre de 2026. Estado general: **IMPLEMENTADO CON
LIMITACIONES**. Se demostró un gateway obligatorio y filtrado independiente del
worker en un laboratorio controlado. **La capacidad de egreso público continúa
BLOQUEADA. La verificación remota y ZAP siguen deshabilitados.**

La rama de Backend está publicada y su hash remoto verificado. No se desplegó
esta fase en la instalación personal ni se autorizó automáticamente la Fase 3.

## A. Inspección inicial — IMPLEMENTADO Y PROBADO

Se localizaron los cuatro repositorios originales, los worktrees de Fases 1/2A/2B,
ramas, remotos, cambios locales, contenedores, volúmenes y redes. Se comprobaron
contra GitHub las bases de Fase 2B:

| Repositorio | Commit de referencia verificado |
|---|---|
| Backend | `7a158b895e750bef8b2b580339a43f863da046ac` |
| Database | `a562e638dfe5b7e8d07eec0e8046e6998f8a2802` |
| Frontend | `0ab0f117651dd058cb2cb70d994999781e18e75f` |
| Context, sin cambios | `caa7ef13b0e2b24848991e32b3c0ee3fc59139c7` |

La revisión del código confirmó persistencia/RLS, versiones, expiración,
revocación, JWT/membresías, servicios FastAPI, política SSRF, DNS controlado,
HTTPS numérico con TLS/Host/SNI y estados React. El servicio operativo conserva
verify=503. El laboratorio anterior compartía un namespace sin rutas externas;
no demostraba un gateway obligatorio separado ni egreso público.

La instalación personal sigue limpia en Backend `fcabbe986f2a4d3dffb259edda0b7245e533ee91`,
Database `b377b5bd8013cc2151f6a1681d168e1df0f5fe42`,
Frontend `4514b6840b428152c1cd875c95e3e55ad81c4196` y el Context indicado.
La comprobación de cierre, **15-11-2026 20:39:56 -06:00**, confirmó PostgreSQL
saludable con `vulntrack_v2_development_pgdata` y publicación exclusiva
`127.0.0.1:55434`. Los procesos originales continúan desde sus checkouts;
web 5173 y /health, /ready, /docs en 8000 devolvieron HTTP 200. No se migró,
restauró, detuvo ni utilizó esa base para pruebas adversariales.

## B. Arquitectura seleccionada — IMPLEMENTADO CON LIMITACIONES

Se seleccionó un gateway pequeño de GET HTTPS acotado, separado del worker,
con tres bridges internos dual-stack: frontal, control y destinos. El worker
solo puede conectar por TCP al gateway en 8443. El gateway consulta una autoridad
y usa DNS numérico fijo y TLS hacia fixtures autorizados.

Se descartó confiar únicamente en un bridge ordinario o variables proxy: no
constituyen una política por operación ni impiden TCP directo. Se evaluaron
filtros globales Windows/VM, sin modificarlos, y una alternativa Linux nativo/VM
dedicada para una futura demostración pública. El gateway actual no es CONNECT,
un túnel general, un verificador ni un ejecutor ZAP.

El documento de arquitectura (referencia local no incluida)
contiene diagrama, alternativas, amenazas, dependencias y límites de confianza.
La elección sigue siendo PROPUESTA técnica; no registra una aprobación conjunta.

## C. Controles implementados — IMPLEMENTADO Y PROBADO

- INPUT/OUTPUT/FORWARD DROP en namespaces propios, IPv4/IPv6, con forwarding
  desactivado en gateway y excepciones exactas por IP/puerto.
- Worker limitado al gateway; sin salida directa a TLS, DNS, servicios internos
  o proxies alternativos. Se vacían variables proxy heredadas en los contenedores.
- Solo Neighbor Discovery IPv6 con hop limit 255; sin permiso ICMP genérico.
- Una operación UUID como única entrada, sin hosts, IPs, puertos, workspace o
  reglas elegibles por el worker.
- Permisos inmutables con scope, versiones, IPs, recursos, vigencia y épocas;
  HMAC/nonce en control, consumo único y denegación de políticas obsoletas.
- Relectura mediante API/RLS de PostgreSQL tmpfs nuevo; leases cortos y pérdida
  de autorización sin conservar un permiso anterior como válido.
- Reutilización del DNS y HTTPS de Fase 2B, comparando resoluciones con el permiso,
  sin nuevas resoluciones silenciosas, redirects o bypass TLS.
- UID 65532, capacidades cero, no-new-privileges, raíz read-only, cgroups y FD
  acotados; sin DB/API/control credentials, docker.sock o mounts del usuario
  dentro del worker. Gateway/autoridad tampoco reciben credenciales de DB.

El helper NET_ADMIN es efímero y solo configura el namespace propio. No se
editaron políticas globales de Windows, WSL host o Docker Desktop. Docker creó
y retiró automáticamente las reglas asociadas a sus redes nuevas; no se
reescribieron cadenas globales del Engine ni daemon.json.

## D. Resultados del laboratorio — IMPLEMENTADO Y PROBADO

El nuevo runner pasó **51 comprobaciones**. Ejerció conexiones permitidas,
negativas TCP/UDP, DNS alternativo/rebinding, TLS, redirects, caducidad,
revocación, concurrencia, conexiones existentes y reinicios/fallos de control.
Las pruebas no se sustituyeron por mocks de red. Se usaron DNS/TLS reales y
servicios propios en redes internas sin contacto con titulares públicos de IP.

| Medición de la ejecución aprobada | Resultado |
|---|---|
| Revocación SQL hasta cierre del TLS activo visto por fixture | 0.591 s |
| Pérdida del broker hasta denegación activa | 1.181 s |
| Máximo observado de lectura/publicación del broker | 0.562 s |
| Descartes OUTPUT worker IPv4 / IPv6 | 13 / 14 paquetes |
| Descartes OUTPUT gateway después de reinicio IPv4 / IPv6 | 2 / 16 paquetes |

Dos conexiones de control confirmaron canarios privados escuchando antes de las
negativas; después recibieron **cero conexiones/datagramas adversariales**. Los
51 identificadores están en resultados técnicos (referencia local no incluida)
y el JSON observado (referencia local no incluida).

Los tiempos son observaciones de una ejecución, no un SLA. El broker contabilizó
146 publicaciones correctas, 64 observaciones de scope invalidado y cuatro
errores de lectura/publicación durante el recorrido con fallos inyectados. Esos
errores de componentes no son pruebas fallidas; se comprobó denegación segura.

## E. Garantías demostradas — IMPLEMENTADO Y PROBADO

Dentro del modelo ensayado, un worker sin privilegios no evitó el gateway al
ignorar la política Python y abrir sockets directamente. Los filtros reales y
sus contadores verifican una barrera adicional a la aplicación. Se observaron
denegaciones de IP/puerto/protocolo, autorización obsoleta y recursos internos.
El Host/SNI original y TLS se observaron en el fixture independiente.

La revocación SQL impidió nuevas conexiones y canceló el socket TLS activo del
gateway. Caducidad, caída del broker o autoridad y épocas nuevas tras reinicio
fallaron cerradas. Un permiso consumido o perteneciente a una época anterior no
recuperó validez. Las denegaciones se repitieron después del reinicio del gateway.

## F. Garantías no demostradas — BLOQUEADO

No se demostró egreso selectivo atravesando un perímetro público real, filtrado
global de Windows/VM, un DNS operativo desplegado o un productor de evidencia
operativa. Tampoco resistencia a administrador Docker/kernel/gateway
comprometidos, aislamiento multitenant desplegado o un supervisor de producción.

El kernel del gateway aplica un techo fijo de IP/puerto del fixture. La decisión
por operación y cancelación pertenece al gateway/control confiable; no se le
atribuye al kernel una semántica SQL/RLS que no tiene. La retirada dinámica de
reglas/conntrack y el aislamiento por job siguen pendientes.

Por esas garantías indispensables pendientes, **egreso público BLOQUEADO**.
Pasar el laboratorio no habilita verificaciones remotas ni análisis.

## G. Protección IPv4/IPv6 — IMPLEMENTADO Y PROBADO

Se probaron ingress IPv6 del gateway y destino TLS IPv6, además del recorrido
IPv4. Sondas directas hacia destinos autorizados y ajenos, puertos distintos,
servicios internos, localhost y DNS/UDP fueron denegadas según la matriz.
Se registraron descartes de ambas familias antes y después del reinicio.
Las 45 comprobaciones anteriores se repitieron e incluyen DNS TCP IPv6 y TLS.
No se desactivó IPv6 para hacer pasar las pruebas.

## H. Redes internas — IMPLEMENTADO CON LIMITACIONES

Se bloquearon canarios que representan Windows, WSL2, PostgreSQL, Backend,
Docker API, metadata, DNS y proxy alternativo. Localhost IPv4/IPv6 tenía listeners
propios y permaneció inaccesible desde el proceso worker. No se montó docker.sock
ni se conectaron las redes del proyecto personal.

Son representaciones controladas, no pruebas contra servicios reales del host.
No se declara certificada la seguridad completa del host o de Docker Desktop.

## I. Integración con autorizaciones — IMPLEMENTADO CON LIMITACIONES

El broker externo conserva las credenciales del PostgreSQL propio. Crea y relee
recursos reales mediante JWT/API/RLS y comprueba el rol vigente owner/editor.
Las políticas vinculan actor, workspace, aplicación, destino, desafío, URL/host,
esquema/puerto, IPs, versiones, vigencias, operación y recursos. React no puede
emitir reglas de red. Cambiar declaración, caducar o revocar invalida la política.

El lease dura 1.5 s desde el inicio de lectura; el gateway consulta cada 0.1 s
con timeout 0.35 s y deadline monotónico local. Existe una ventana de observación
y planificación. El cierre de TLS es activo, **no instantáneo en kernel**; no se
pueden retirar bytes ya enviados. No hay keep-alive o túneles reutilizables.

Toda procedencia permanece laboratory. El desafío no se convierte en verified;
verify conserva 503, cero DNS e intentos, `control_verified=false` y
`execution_enabled=false`. El laboratorio asigna operaciones a un worker propio;
el diseño operativo futuro requiere identidad autenticada y aislamiento por job,
evidencia real y autorización operativa separada.

## J. Backend — IMPLEMENTADO CON LIMITACIONES

Se añadieron módulos de laboratorio, broker externo, runner reproducible, 57
casos unitarios y documentación/checklist. Se reutilizan los componentes DNS/TLS
existentes. No cambian app, rutas operativas, SQL, dependencias ni contratos.
OpenAPI conserva sus 44 operaciones y su SHA-256 de Fase 2B.

No hay endpoint público de gateway/análisis ni un flag que habilite verificación
remota o ZAP. El prototipo solo se inicia desde el runner de laboratorio.

## K. Database — IMPLEMENTADO Y PROBADO

Sin cambios de SQL, ORM o migraciones; D-002 se conserva. Se utilizó Database
`a562e638dfe5b7e8d07eec0e8046e6998f8a2802`, compatible con SQL008 de Backend.
Se creó un checkout detached hermano para las pruebas heredadas, sin rama nueva.
Bootstrap, migración compatible, RLS, roles y evidencias se regresaron en bases
nuevas. La instalación personal sigue en su commit y volumen anteriores.

Reinicio y backup/restore en OTRO volumen nuevo conservaron IDs, fechas,
evidencias y FORCE RLS; también se comprobó compatibilidad de lectura con código
estable/Fase 2A. Los volúmenes de ensayo retenidos y detenidos son
`vulntrack_v2_verification_test_459e72960c_pgdata` y
`vulntrack_v2_verification_test_restore_459e72960c_pgdata`.

## L. Frontend — IMPLEMENTADO Y PROBADO

Frontend permanece en `0ab0f117651dd058cb2cb70d994999781e18e75f`, sin cambios ni
rama nueva. Pasaron sus 96 pruebas y seis casos de navegador por recorrido,
incluidos estados reales, accesibilidad Axe y responsive 1440/390 px.
Baseline continúa deshabilitado y ningún fixture aparece como control real.

## M. Pruebas y regresiones — IMPLEMENTADO Y PROBADO

Todos estos resultados corresponden a ejecuciones de esta misión:

| Recorrido | Resultado final |
|---|---|
| Backend, instalación existente compatible | 734 passed, 0 failed, 0 skipped; 212.85 s |
| Backend, instalación nueva | 734 passed, 0 failed, 0 skipped; 209.31 s |
| SQL por recorrido | 120 comprobaciones repetidas |
| SQL003 adversarial separado | 27 comprobaciones en tmpfs propio |
| React | 96 pruebas en ocho archivos |
| Navegador, existente / nueva | 6 / 6 passed; 21.0 / 18.9 s |
| Nuevo laboratorio Fase 2C | 51 comprobaciones |
| Laboratorio Fase 2B repetido | 45 comprobaciones |
| Postman, existente | 288 aserciones, 75 envíos HTTP, cero fallos |
| Postman, nueva | 303 aserciones, 78 envíos HTTP, cero fallos |
| Postman, contrato persistente desechable | 200 aserciones, 53 envíos HTTP, cero fallos |
| Persistencia/reinicio/restore/compatibilidad | Siete comprobaciones |
| Ruff, pip check, TypeScript, ESLint y build | Sin fallos |
| Git diff --check, hashes de imagen/fuentes y revisión de secretos | Sin fallos |

Los 734 incluyen **57 casos nuevos de política/protocolo** y **677 anteriores
repetidos**. Los recorridos repiten escenarios, no son 1468 escenarios únicos.
React/navegador no agregaron casos nuevos. Las 51 comprobaciones del nuevo runner
y 45 del anterior se separan porque sus coberturas se solapan.

Postman no cambió; sus conteos varían por polling. El validador confirmó 71
solicitudes definidas, 23 cuerpos Pydantic y 49 ejemplos positivos. Las respuestas
previamente permisivas mantienen esa limitación. Los flujos cubren registro/login,
JWT, workspaces, aplicaciones, autorizaciones, desafíos, RLS, CPE/CVE,
recomendaciones y dashboard con NVD fixture.

La primera pasada Backend encontró cuatro errores por faltar Database como
checkout hermano. Se corrigió la preparación y se repitieron ambos recorridos
completos; no se omitieron ni relajaron pruebas. Persisten advertencias heredadas
de TestClient/Newman. Las pruebas de salida pública, firewall global y Linux/VM
alternativo no se ejecutaron: son garantías pendientes, no skips de pytest.

[Resumen sanitizado y capturas](fase2c-seguridad/evidencia/RESULTADOS.md).
Logs completos privados: `/home/USUARIO/VulnTrack-security-phase2c/vuln-track-backend/api-tests/v2/.artifacts/phase2c/`.

## N. Estado de ZAP — BLOQUEADO

No se integró, instaló, activó o ejecutó ZAP; no se habilitó análisis ni
verificación remota. No hubo escaneos externos o solicitudes a dominios públicos
de prueba. Las descargas de imágenes/paquetes fueron preparación confiable,
fuera del plano de datos; no cuentan como prueba de egreso público selectivo.
El checklist actualizado (referencia local no incluida)
mantiene pendientes operativos y decisiones humanas explícitas.

## O. Ramas y commits — IMPLEMENTADO Y PROBADO

Única rama nueva: Backend `feat/v2-security-egress-phase2c`, base Fase 2B.
Commit publicado:
[f571ea8d0f943900b3ddde900d6f7011726e8f56](https://github.com/Jesel8D/vuln-track-backend/commit/f571ea8d0f943900b3ddde900d6f7011726e8f56).

Push normal, hash remoto coincidente y worktree limpio. Database/Frontend/Context
y ramas previas permanecen sin cambios. No hubo force push, merge, PR automático,
modificaciones a main/rudi o despliegue personal. Los 15 archivos publicados
pasaron revisión contra valores privados de la configuración personal y claves
privadas. No se publicaron dumps, tokens, artefactos privados ni datos personales.

## P. Límites Windows/WSL2 — IMPLEMENTADO CON LIMITACIONES

Entorno observado: Docker Desktop 4.99.0.245822, Engine/CLI 29.9.3 y kernel WSL2
6.18.58.1-microsoft-standard-WSL2. El filtro del proceso/contenedor no equivale al
bridge del Engine, la red de la VM o el firewall Windows. No se midió egreso
público atravesando esas últimas capas ni se cambiaron políticas globales.

La arquitectura alternativa propone Linux nativo o VM dedicada aislada de datos
personales, con worker-gateway interno, DNS autorizado, interfaz externa y
filtrado independiente por job. Requiere ensayos reales del perímetro, rutas,
revocación/conntrack, reinicio y fallos. Es diseño pendiente, no una garantía
probada ni una instalación realizada. El host/daemon/gateway siguen siendo
autoridades de confianza.

## Q. Requisitos para una futura Fase 3 — PENDIENTE

1. Elegir y revisar conjuntamente plataforma, responsables, permisos operativos,
   método/alcance de verificación, vigencia, renovación, cuotas y retención.
2. Demostrar egreso selectivo real a destinos controlados a través del perímetro
   elegido, sin rutas alternativas, en IPv4/IPv6 y con DNS autorizado.
3. Autenticar el canal y vincular cada permiso a worker/job/workspace, con
   aislamiento entre trabajos y evidencias operativas confiables. Ningún fixture
   puede convertirse automáticamente en permiso público o control de dominio.
4. Implementar supervisor y estado duradero de jobs, límites de tráfico,
   cancelación/revocación, retirada de conexiones y recuperación segura.
5. Repetir la revisión adversarial integrada, observabilidad saneada y ensayos de
   backup/restore/fallo sobre esa plataforma. Decidir por separado una futura
   actualización personal con respaldo nuevo y reversión probada.
6. Obtener una decisión explícita posterior antes de cualquier integración ZAP
   y definir el alcance autorizado de cada ejecución. Esta entrega no concede
   ese permiso ni inicia la siguiente fase.

Documentación reproducible: laboratorio (referencia local no incluida).
Metadatos de entrega: .setup/security-phase2c-delivery.json (referencia local no incluida).
