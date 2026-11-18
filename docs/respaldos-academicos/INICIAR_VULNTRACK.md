> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 — ejecución persistente en WSL2

Verificado el 15 de noviembre de 2026. **VulnTrack está iniciado con PostgreSQL saludable y perfil development en fase ready.** El launcher quedó ejecutándose en segundo plano; puedes detenerlo desde otra terminal Ubuntu con el comando stop de esta guía.

## A. Estado

Carpeta de ejecución: **/home/USUARIO/VulnTrack**, dentro de Ubuntu-24.04, usuario mz. Los cuatro repositorios son carpetas hermanas en el sistema de archivos Linux.

La recuperación previa en %PERFIL%\Documents\VulnTrack se conserva. Para ejecutar, utiliza los checkouts y dependencias Linux.

| Repositorio | Rama | Commit |
|---|---|---|
| Backend | feat/v2-stabilization | fcabbe9 |
| Database | feat/v2-persistent-development-database | b377b5b |
| Frontend | feat/v2-registration-web | 4514b68 |
| Context | docs/mvp-context-consolidation | caa7ef1 |

Los origin son https://github.com/Jesel8D/NOMBRE-DEL-REPOSITORIO.git. Los clones Linux se recuperaron directamente de GitHub y sus HEAD coinciden con los commits esperados. No se modificaron código, contratos, locks o ramas existentes ni se crearon commits, merges o pushes.

Preparación completada:

- WSL 3.0.5 y Ubuntu 24.04.5 LTS funcionando en WSL2.
- Docker Desktop 4.99.0 con integración Ubuntu habilitada; cliente y servidor Linux 29.9.3; Compose 5.7.0.
- Python 3.12.3 y venv en /home/USUARIO/VulnTrack/vuln-track-backend/.venv.
- requirements-dev.lock instalado y Backend instalado con --no-deps -e .; pip check correcto. FastAPI, SQLAlchemy, Alembic y Psycopg disponibles.
- Node Linux 24.18.0 y npm 11.16.0 en /home/USUARIO/.local/opt/node-v24.18.0-linux-x64. Archivo oficial verificado por SHA-256.
- Backend: 221 pruebas unitarias aprobadas. La ayuda del launcher funciona en Linux.
- Frontend: npm ci, compilación correcta y 77 pruebas aprobadas en Ubuntu.
- Pruebas en memoria completadas. Tras el arranque real, registro y login web, health, readiness, Swagger y ReDoc respondieron HTTP 200 desde Windows; CORS coincide con el Frontend.
- Las seis fuentes SQL coinciden por SHA-256 con el manifiesto del Backend.
- Imagen PostgreSQL preparada: postgres:16.15-alpine@sha256:0cefaabd882e42bcb2a2f3dc56d3ce4770560ba62fd7f197f1a2e008fd5731ab.
- Compose validado estáticamente. Puertos 55434, 8000 y 5173 disponibles al comprobarlos tanto en Windows como en Ubuntu.
- Instalación persistente creada mediante el launcher original: contenedor saludable, volumen propio y PostgreSQL publicado solo en 127.0.0.1:55434. La base tiene 0 usuarios, 0 workspaces y 0 aplicaciones.

No faltan herramientas para esta ejecución local. Docker Desktop debe estar abierto y su motor listo. El launcher requiere Linux: se utiliza desde Ubuntu sin modificarlo.

## B. Cómo iniciar, detener, reiniciar y respaldar

Si ya ves el prompt mz@mz:...$, estás en Ubuntu. Desde PowerShell puedes entrar con:

    wsl --distribution Ubuntu-24.04 --user mz

La aplicación ya está iniciada. Para arranques posteriores, después de detenerla, ejecuta en **Ubuntu**:

    export PATH="/home/USUARIO/.local/opt/node-v24.18.0-linux-x64/bin:$PATH"
    cd /home/USUARIO/VulnTrack/vuln-track-backend
    .venv/bin/python -m scripts.v2_development up --frontend /home/USUARIO/VulnTrack/vuln-track-frontend --nvd-mode fixture

Mantén abierta esa terminal. El comando inicia PostgreSQL, FastAPI, React/Vite y el worker CVE dentro de FastAPI. En el primer arranque crea su instalación nueva mediante el bootstrap verificado. No interrumpas la primera inicialización SQL. No necesitas ejecutar Alembic, SQL ni otro worker por separado.

Database se encuentra automáticamente en la carpeta hermana /home/USUARIO/VulnTrack/vuln-track-database.

| Recurso | Valor predeterminado |
|---|---|
| Perfil | development |
| Proyecto Compose | vulntrack-v2-development |
| Contenedor | vulntrack-v2-development-postgres |
| Volumen persistente | vulntrack_v2_development_pgdata |
| PostgreSQL | 127.0.0.1:55434 |
| Configuración y logs privados | /home/USUARIO/VulnTrack/vuln-track-backend/.local/development/ |

El launcher verifica la propiedad de volumen, contenedor y red y rechaza colisiones. Si falla la inicialización, conserva los recursos y logs para revisarlos.

Para detener, pulsa **Ctrl+C** en la terminal de inicio. También puedes usar otra terminal Ubuntu, después de inicializar la instalación:

    cd /home/USUARIO/VulnTrack/vuln-track-backend
    .venv/bin/python -m scripts.v2_development stop
    .venv/bin/python -m scripts.v2_development status

Se detienen API, Vite y PostgreSQL, conservando el volumen. Para reiniciar, repite el bloque de inicio, incluido export PATH. Después de un cierre abrupto de terminal, ejecuta stop antes de otro up.

No utilices down -v, volume prune ni elimines el volumen.

Para respaldar una instalación inicializada:

    cd /home/USUARIO/VulnTrack/vuln-track-backend
    .venv/bin/python -m scripts.v2_development backup

El comando imprime la ruta real de .local/development/backups/FECHA-ID/database.dump y genera manifest.json con SHA-256. Si PostgreSQL estaba detenido, el backup lo inicia; ejecuta stop después si deseas dejarlo detenido.

Conserva dump y manifiesto juntos en un respaldo privado fuera de este disco. Guarda también la configuración privada para recuperar la instalación original. Son archivos sensibles: no los subas a Git. La restauración documentada exige un perfil y volumen nuevos.

## C. URLs

Disponibles ahora. Usa siempre 127.0.0.1 para coincidir con CORS.

| Acceso | URL |
|---|---|
| Frontend / dashboard | http://127.0.0.1:5173/ |
| Crear cuenta | http://127.0.0.1:5173/crear-cuenta |
| Login | http://127.0.0.1:5173/login |
| Aplicaciones | http://127.0.0.1:5173/aplicaciones |
| Swagger | http://127.0.0.1:8000/docs |
| ReDoc | http://127.0.0.1:8000/redoc |
| OpenAPI | http://127.0.0.1:8000/openapi.json |
| Health | http://127.0.0.1:8000/health |
| Readiness, incluida la base | http://127.0.0.1:8000/ready |

El launcher configura VITE_API_URL=http://127.0.0.1:8000 y CORS para http://127.0.0.1:5173. No coloques secretos en variables VITE_*.

Las rutas y configuración están verificadas en el código. Los servicios reales respondieron desde Windows, incluidas las pantallas de registro y login y la comprobación de readiness con PostgreSQL. El recorrido visual de creación de cuenta todavía está pendiente.

## D. Primera prueba

1. En Crear cuenta, elige un usuario ASCII de 3–80 caracteres y una contraseña privada de 15–128 caracteres.
2. Inicia sesión en /login.
3. Comprueba el workspace personal creado automáticamente y su dashboard vacío.
4. En Aplicaciones, registra un nombre y descripción de prueba. Anota el nombre o ID.
5. Detén con Ctrl+C, ejecuta nuevamente el bloque de inicio e inicia sesión si es necesario. Comprueba que la misma aplicación y workspace permanecen.

## E. Limitaciones

- El primer arranque y la conexión real con PostgreSQL están verificados; todavía falta el recorrido completo de registro, login y uso con una cuenta.
- El launcher original inicializó una base nueva en su volumen dedicado. No se adoptaron ni migraron bases ajenas y no se borraron volúmenes.
- La instalación nueva está vacía y no contiene seeds de demo. Git no recupera la base de la computadora Linux anterior; se necesita un respaldo explícito para esos datos.
- NVD conserva fixtures, sin consultas live. Sus capturas no representan un análisis actual de sistemas reales.
- ZAP permanece deshabilitado. No se realizaron escaneos externos ni se habilitaron servicios en interfaces externas.
- Pendientes: registro/login contra esta base, recorrido de navegador, E2E, persistencia de datos de usuario tras reinicio, backup y restore.
- Backend mostró un aviso heredado de deprecación Starlette/TestClient. Se conservaron las dependencias bloqueadas.
- El launcher generó los secretos en .local/development, ignorados por Git. Los respaldos también quedan en esa carpeta privada.

Referencia operativa: /home/USUARIO/VulnTrack/vuln-track-backend/docs/V2_PERSISTENT_DEVELOPMENT.md.

