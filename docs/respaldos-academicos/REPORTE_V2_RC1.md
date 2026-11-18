> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 — MVP Release Candidate 1

**2026-11-15 · IMPLEMENTADO CON LIMITACIONES.** GO técnico para revisión académica
local controlada del flujo V2, sujeto a ratificación humana del alcance. No es
aprobación de entrega final ni producción. La instalación habitual se conserva.

## A. Estado inicial de repositorios — IMPLEMENTADO Y PROBADO

Los cuatro repositorios de `/home/USUARIO/VulnTrack` estaban limpios y se mantuvieron
en sus ramas/commits. No se modificaron main, rudi ni ramas MVP-B.

| Repositorio habitual | Rama | Commit protegido |
|---|---|---|
| Backend | feat/v2-stabilization | fcabbe986f2a4d3dffb259edda0b7245e533ee91 |
| Database | feat/v2-persistent-development-database | b377b5bd8013cc2151f6a1681d168e1df0f5fe42 |
| Frontend | feat/v2-registration-web | 4514b6840b428152c1cd875c95e3e55ad81c4196 |
| Context | docs/mvp-context-consolidation | caa7ef13b0e2b24848991e32b3c0ee3fc59139c7 |

development siguió encendida: volumen `vulntrack_v2_development_pgdata`,
PostgreSQL en 127.0.0.1:55434, API 8000 y web 5173. No se utilizó para pruebas,
restauraciones, migraciones ni cambios de usuarios/workspaces.

## B. Commits seleccionados — IMPLEMENTADO Y PROBADO

Se verificaron referencias remotas y genealogía antes de seleccionar las bases:

| Componente | Base exacta | Rama remota comprobada |
|---|---|---|
| Backend | f571ea8d0f943900b3ddde900d6f7011726e8f56 | feat/v2-security-egress-phase2c |
| Database | a562e638dfe5b7e8d07eec0e8046e6998f8a2802 | feat/v2-security-verification-database |
| Frontend | 0ab0f117651dd058cb2cb70d994999781e18e75f | feat/v2-security-analysis-web-phase2b |
| Context conservado | caa7ef13b0e2b24848991e32b3c0ee3fc59139c7 | docs/mvp-context-consolidation |

El histórico Postman `63315644121bb0074cd18f5d2da11720358caea0` es ancestro del
Backend elegido. La colección ya estaba integrada y evolucionada; no se hizo
merge redundante. Los commits **finales RC1** están en Q y el manifiesto de entrega.

## C. Compatibilidad Backend/Database/Frontend — IMPLEMENTADO Y PROBADO

SQL canónico/paquetizado coincide por hashes; modelos y migraciones corresponden
a los catálogos estrictos. Se ejecutaron rutas existing/new en tmpfs, SQL, API,
Postman y navegador con los tres repositorios juntos. SQL008 y la revisión de
verificación permanecen separadas del head legado 0004; API recibe rol mínimo,
contexto HMAC por transacción y RLS. Frontend consume los DTO actuales de
workspaces, aplicaciones, tecnologías, CPE/CVE, hallazgos, recomendaciones y
readiness. Matriz completa (referencia local no incluida).

## D. Entorno RC1 — IMPLEMENTADO Y PROBADO

| Recurso | Valor |
|---|---|
| Directorio | /home/USUARIO/VulnTrack-rc1 |
| Perfil | rc1 |
| Volumen PostgreSQL | vulntrack_v2_rc1_pgdata |
| Contenedor | vulntrack-v2-rc1-postgres |
| PostgreSQL loopback | 55438 |
| API / Swagger | http://127.0.0.1:8004/docs |
| Web | http://127.0.0.1:5177 |
| Configuración y claves privadas | Backend .local/rc1/ |
| NVD / worker | fixture explícito / habilitado dentro de API |

Windows/WSL2 Ubuntu 24.04, Python 3.12.3, Node 24.18.0, PostgreSQL 16.15 y Chromium
Playwright. El launcher ya soportaba recursos alternativos; se verificó antes de
arrancarlo. Se conservaron locks y todos los entornos de fases anteriores.

## E. Problemas encontrados — IMPLEMENTADO Y PROBADO

Se reprodujeron ocho grupos de defectos: procedencia fixture invisible;
dashboard con resumen anterior tras mutaciones; meses desplazados por zona
horaria; contraste insuficiente de textos pequeños/estados; semántica del login
móvil; jerarquía CVE/landmarks indistinguibles; desbordamiento tablet de 768 px; y
falso puerto ocupado al reiniciar por conexiones TCP TIME_WAIT.

También hubo supuestos incorrectos del test (201, retorno a ruta protegida,
sesión en memoria, selector, espera de menú) y selección incorrecta del ejecutable
Chrome histórico. Se corrigieron las pruebas/configuración. La repetición alcanzó
la cuota de login normal; ahora se respeta Retry-After. Los fallos intermedios
están registrados privadamente y no se presentan como ejecuciones finales verdes.

## F. Correcciones realizadas — IMPLEMENTADO Y PROBADO

- Aviso visible de NVD fixture mediante variable pública de modo, sin secretos Vite.
- Refetch del dashboard al abrir y formato UTC de períodos mensuales; se compara con API.
- Colores de texto demostrados más oscuros, h1 de formulario visible, h3 CVE y landmarks rotulados.
- Columna acotada, título divisible y wrap en tablet; sin rediseño global.
- Sonda de puerto con SO_REUSEADDR; prueba real TCP rechaza listener activo y acepta TIME_WAIT tras cierre.
- Herramientas RC1 con guardas de perfil/volumen/puertos/proceso/modo, E2E y recuperación reproducibles.

Sin cambios de endpoints, modelos, SQL, RLS, SSRF, reglas del gateway o semántica
CVE. No se habilitaron capacidades bloqueadas para pasar pruebas.

## G. Pruebas Backend — IMPLEMENTADO Y PROBADO

**755 aprobadas, 0 fallidas/omitidas** por existing y **755/0/0** por new. Son la
misma suite repetida sobre dos bootstraps, no 1510 casos únicos:734 conservadas
y 21 nuevas (19 guardas RC1 y 2 TCP). Duraciones196.13 s y 196.10 s. Ruff/pip check
correctos. Una deprecación Starlette/httpx queda documentada; no se cambian locks
para ocultar avisos.

## H. Pruebas Database — IMPLEMENTADO Y PROBADO

120 comprobaciones SQL por ruta (55+21+6+10+4+24), más27 llamadas adversariales
SQL003 en otra tmpfs. Bootstrap limpio, roles, FORCE RLS, migraciones, concurrencia,
transacciones y recuperación cubiertos por SQL/integración. Sin cambios Database;
no se creó rama RC1 innecesaria. No se usó la base personal.

## I. Pruebas Frontend — IMPLEMENTADO Y PROBADO

96 unitarias/componentes en8 archivos, TypeScript, ESLint y build correctos.
Suite previa V2:6 aprobadas por existing y6 por new, 0 fallidas/omitidas. RC1:
375/768/1280/1440px, sin desbordamiento y cero violaciones axe en las vistas
evaluadas, incluidas CPE/CVE/recomendaciones y destino expirado seleccionado.
No equivale a certificación de accesibilidad ni prueba de todos los navegadores.

## J. Pruebas Postman — IMPLEMENTADO CON LIMITACIONES

Validación OpenAPI 3.1 / Postman 2.1:44 operaciones,71 solicitudes definidas,23 cuerpos
Pydantic y49 ejemplos positivos. Newman: existing 283 aserciones / 74 HTTP;
new 288 / 75 HTTP; contrato persistente 185 / 50 HTTP, todos sin fallos. El último usa
tmpfs propia y verifica las guardas, no development. Los conteos HTTP varían por
polling; no se suman como casos únicos. Algunas respuestas V2 conservan esquemas
permisivos; ejemplos no son validadores. SHA OpenAPI:
`082d1c6a0b84564b8814ede1724b5d72e54249d5fd9893d65149b0af00133b16`.

## K. Recorridos de navegador — IMPLEMENTADO Y PROBADO

9 casos de creación/aislamiento/seguridad/UX/error y 1 B tras reinicio + 1 B tras
restore, todos aprobados sin omisiones. A usa formularios normales, confirmación
CPE explícita, API y worker reales sobre capturas, revisión conservadora y medida
manual. C cambia cuentas, IDs y campos falsificados, comprueba404/422 y pool sin
fuga. D emite/revoca desafíos y espera caducidad real de8 s; verify 503, intentos 0,
sin Baseline. E comprueba carga/error de red/reintento200 real sin tarjetas falsas.
No se abrieron enlaces externos.

No ejecutados: suites UI V1 cuyos paths App V2 ya no monta, otros navegadores,
móvil nativo, NVD live, verificación remota o escaneo externo. Se conservan los
tests históricos y V1 permanece cubierto por regresión Backend.

## L. Persistencia — IMPLEMENTADO Y PROBADO

Se detuvieron API/web/PostgreSQL propios RC1, se reiniciaron con el mismo volumen
y se compararon DTO completos, IDs, fechas, CPE, consulta, hallazgo reviewed y
recomendación pending. B confirmó login e historial. Mismo perfil conserva sus
claves/JWT. Recargar una pestaña exige login porque el token vive en memoria; no
es pérdida de los datos de PostgreSQL.

## M. Backup y restauración — IMPLEMENTADO Y PROBADO

13 comprobaciones de recuperación aprobadas: dump consistente privado 0600,
checksum y exclusión Git; restauración transaccional en otro perfil/volumen nuevo,
puertos 55439/8005/5178, catálogo/roles/RLS exactos, mismos DTO/fechas y aislamiento.
El secreto JWT cambia: token antiguo 401, contraseña normal válida. Navegador B
funciona contra la copia. Se detuvo la copia y se conservaron ambos volúmenes;
RC1 quedó encendida. Resultado saneado (referencia local no incluida).

El dump contiene hashes de usuarios y HMAC: es sensible y **no se publicó**.
Su ruta privada y SHA están en el resultado. No se copiaron dump/configuración/
accesos al paquete. No sustituye un backup externo cifrado ante pérdida de disco.

## N. Seguridad y aislamiento — IMPLEMENTADO CON LIMITACIONES

RLS/roles/workspaces/IDs/pool probados en SQL/API/navegador y después de restore.
Se repitieron45 comprobaciones de laboratorio 2B y51 de gateway 2C, con DNS/TLS
fijado, filtros IPv4/IPv6 independientes de Python, revocación, caducidad,
concurrencia y recuperación. En esta misión: revocación→cierreTLS0.536s,
pérdida broker→denegación1.205s; son observaciones, no SLA. Cero tráfico
adversarial a canarios prohibidos. No se cambió firewall global ni daemon Docker.

**BLOQUEADO:** egreso público y verificación técnica remota. El gateway interno
no demuestra control de un dominio externo ni certifica un perímetro operativo.
Declaración vigente y evidencia de laboratorio nunca equivalen a verified.

## O. Estado de ZAP — BLOQUEADO

Baseline permanece disabled; no se ejecutó ZAP ni se crearon hallazgos o
ejecuciones ficticios. No hay autorización operativa para activarlo en RC1.

## P. Demostración académica — IMPLEMENTADO CON LIMITACIONES

Dataset ficticio preparado y guion manual de13 min (referencia local no incluida).
Aplicación DEMO, URL de inventario sin conexión, Cisco RV340 firmware 1.0.0.33,
CPE confirmado explícitamente, CVE-2017-6784 con AND original; aplicabilidad
**undetermined**, revisión reviewed y recomendación manual pending. NVD fixture
visible en todas las vistas autenticadas. Dashboard refleja el workspace real;
cero abiertos después de revisar no significa que sea seguro.

Acceso privado en Backend `.local/rc1/demo-access.json`; no contraseña pública.
Crear una cuenta nueva por UI es posible. Repetir aceptación genera otro dataset
y archiva accesos anteriores sin borrar sus datos. Ver guía antes de repetirla.

## Q. Ramas y commits publicados — IMPLEMENTADO Y PROBADO

El manifiesto final `.setup/v2-rc1-delivery.json` contiene los hashes completos
locales/remotos y el conjunto compatible. Se publicaron únicamente las dos ramas
RC1 con cambios reales, por push normal y después de las verificaciones. No hubo
force push, merge, cambios main/rudi/MVP-B ni despliegue Internet.

| Repositorio | Rama compatible | Commit final remoto verificado |
|---|---|---|
| Backend RC1 | release/v2-rc1-integration | [4453ae0d9b25de361947113f7c61c1b4797385ab](https://github.com/Jesel8D/vuln-track-backend/commit/4453ae0d9b25de361947113f7c61c1b4797385ab) |
| Frontend RC1 | release/v2-rc1-web | [4674cbcd9f46ad0e9e587b0db630b9f51a990a86](https://github.com/Jesel8D/vuln-track-frontend/commit/4674cbcd9f46ad0e9e587b0db630b9f51a990a86) |
| Database sin cambios | feat/v2-security-verification-database | a562e638dfe5b7e8d07eec0e8046e6998f8a2802 |
| Context sin cambios | docs/mvp-context-consolidation | caa7ef13b0e2b24848991e32b3c0ee3fc59139c7 |

Los worktrees RC1 quedan limpios. Solo Backend y Frontend necesitaron ramas y
commits nuevos. Manifiesto de entrega (referencia local no incluida).

## R. Decisiones pendientes — PENDIENTE

Jesel y Rudi deben ratificar alcance académico definitivo, convivencia V1/V2,
qué demo satisface la rúbrica y si móvil queda fuera. Dispositivos V1 usan
propiedad individual; aplicaciones V2 usan workspace/RLS. No se fusionaron estos
conceptos ni se creó arquitectura nueva. App V2 no monta las pantallas V1;
Backend conserva sus contratos históricos. Móvil nativo no está implementado.

La aprobación individual previa de Jesel no equivale a ratificación de Rudi ni
decisión global. Si el MVP exigido sigue siendo dispositivos, esta RC1 web V2
necesita aprobación explícita antes de sustituirlo. Actualizar development requiere
otra autorización/ventana; no forma parte de esta entrega.

## S. Pasos para ejecutar RC1 — IMPLEMENTADO Y PROBADO

Abrir Ubuntu con `wsl -d Ubuntu-24.04`. Consultar primero estado; si ya corre,
abrir5177. Si está detenida:

```bash
export PATH="/home/USUARIO/.local/opt/node-v24.18.0-linux-x64/bin:$PATH"
cd /home/USUARIO/VulnTrack-rc1/vuln-track-backend
.venv/bin/python -m scripts.v2_development up --profile rc1 \
  --pg-port 55438 --api-port 8004 --web-port 5177 \
  --frontend ../vuln-track-frontend --nvd-mode fixture
```

Parada: `.venv/bin/python -m scripts.v2_development stop --profile rc1`.
Backup: mismo módulo, `backup --profile rc1`. Restore solo en otro perfil/volumen
nuevo con dump/manifiesto verificados. Procedimiento completo, dependencias,
credenciales privadas y solución de errores en
[INICIAR_VULNTRACK_RC1.md](INICIAR_VULNTRACK_RC1.md) y
guía local (referencia local no incluida). No usar comandos sin `--profile rc1`
para esta candidata ni sobrescribir el volumen protegido.

## T. Recomendación GO / NO-GO — IMPLEMENTADO CON LIMITACIONES

**GO para revisión académica local controlada de VulnTrack V2 RC1**: integración
real del conjunto compatible, regresión nueva, UX básica, datos conservadores,
aislamiento y recuperación comprobados; demo reproducible y límites explícitos.

**NO-GO para producción, entrega final aprobada, escaneo externo, verificación
remota o sustitución automática del MVP original.** Ratificación de Jesel/Rudi,
móvil y decisiones de alcance siguen pendientes; egreso público y ZAP bloqueados.
Las pruebas no sustituyen decisiones humanas.

Documentación fuente: [integración](rc1/docs/V2_RC1_INTEGRATION_REPORT.md),
matriz (referencia local no incluida),
limitaciones (referencia local no incluida),
checklist (referencia local no incluida),
aceptación Frontend (referencia local no incluida).
