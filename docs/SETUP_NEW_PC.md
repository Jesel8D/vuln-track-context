# Recuperar VulnTrack desde otra computadora

Registro 2026-11-08. La aprobación de alcance es individual de Jesel; Rudi sigue pendiente. Esta guía recupera ramas independientes: no integra V2 ni habilita producción. Ejemplos Bash para Linux/macOS/WSL; adapte instalación y rutas al sistema real. No se necesitan archivos del equipo anterior para código y contexto publicados; secretos operativos se generan/configuran de nuevo.

## 1. Git, SSH y Docker

Instalar Git desde [git-scm.com/downloads](https://git-scm.com/downloads); en Debian/Ubuntu puede usarse `sudo apt update` y `sudo apt install git`. Comprobar `git --version`. Para Windows puede utilizarse WSL con herramientas compatibles; no mezclar rutas Windows y Linux en el mismo entorno de Python. Configurar nombre y correo de autor propios antes de crear commits, sin copiar identidad privada de otro equipo.

Configurar SSH según [GitHub: generación de una clave](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent). Si no existe una clave apropiada, generar una nueva con `ssh-keygen -t ed25519` y agregar **solo la clave pública** a GitHub. No sobrescribir una clave existente, publicar la privada ni copiar secretos al repositorio. Verificar huella del host antes de aceptarlo conforme [huellas SSH de GitHub](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints).

```bash
ssh -T git@github.com
```

GitHub responde autenticación correcta y que no proporciona shell; ese comando puede devolver código 1 aunque autentique. Instalar Docker Engine/Compose o Docker Desktop conforme al sistema; verificar daemon/permisos propios sin detener servicios existentes:

```bash
docker version
docker compose version
docker ps -a
```

## 2. Clonar cuatro repositorios sin mezclar ramas

Ruta configurable, no la del equipo anterior:

```bash
VULNTRACK_ROOT="$HOME/Projects/VulnTrack"
mkdir -p "$VULNTRACK_ROOT"
cd "$VULNTRACK_ROOT"
git clone --branch docs/mvp-context-consolidation git@github.com:Jesel8D/vuln-track-context.git context-general-ia
git clone --branch feat/backend-v1-assets git@github.com:Jesel8D/vuln-track-backend.git backend
git clone --branch feat/postgresql-database-v1 git@github.com:Jesel8D/vuln-track-database.git database
git clone git@github.com:Jesel8D/vuln-track-frontend.git frontend
```

Usar directorio nuevo si ya existen esas carpetas; no reemplazarlas. Frontend era remoto vacío al verificar: el aviso de repositorio vacío es esperado y no requiere inventar commit ni inicializar React. No modificar main. Leer `context-general-ia/docs/PROJECT_MASTER_CONTEXT.md`, `MVP_APPROVAL_RECORD.md`, `VULNTRACK_MVP_CONTRACT.md`, `PROJECT_HANDOFF.md` y `REMOTE_BACKUP_INVENTORY.md`. El directorio research contiene los 31 informes importados y su manifiesto.

Verificar hashes/ramas frente al inventario y al reporte final de publicación; no basta un status limpio:

```bash
git -C backend rev-parse HEAD
git -C database rev-parse HEAD
git -C context-general-ia rev-parse HEAD
git -C backend ls-remote origin refs/heads/feat/backend-v1-assets
git -C database ls-remote origin refs/heads/feat/postgresql-database-v1 refs/heads/rudi refs/heads/audit/database-v2-security-validation
git -C context-general-ia ls-remote origin refs/heads/docs/mvp-context-consolidation refs/heads/dev/jesel refs/heads/rudi
```

Referencias de recuperación: backend `2cb124d7cc41e22a62a305760826240f9208c257`, Database V1 `b493fd17e9290ca94fa085d49230073ed1837e1a`, Database Rudi `54b7644467a1096c011daf3e5c23d7f9a7773f93`, auditoría `2c9723146e910aa51211958556961b4da7019ce3`. El hash final de Context se resuelve con ls-remote; el inventario identifica el corte publicado y explica su autoverificación.

## 3. Ramas de Rudi y auditoría en worktree independiente

```bash
git -C backend branch -r
git -C database branch -r
git -C context-general-ia branch -r
git -C database show origin/rudi:docs/DATABASE_V2_PLAN.md
git -C database worktree add --track -b audit/database-v2-security-validation \
  "$VULNTRACK_ROOT/database-v2-audit" origin/audit/database-v2-security-validation
```

En una clonación nueva la rama local audit todavía no existe; si ya existe, inspeccionar `git worktree list` antes de ejecutar ese comando. No hacer merge de audit/rudi con V1 ni copiar SQL004 a V1. La rama backend rudi contiene README y gitignore, no sustituye Backend V1.3. La ratificación de Rudi no puede deducirse de que sus ramas sean públicas.

El parche de diagrama preexistente está respaldado en `research/local-preservation/`; no se aplica por defecto. Revisar README y `git apply --check` únicamente en copia adecuada si después se decide recuperar ese cambio.

## 4. Python y dependencias reales del backend

Preparar Python 3.12 con venv según el sistema; `pyproject.toml` exige >=3.12 y el snapshot se probó históricamente con 3.12. No instalar dependencias en el Python global:

```bash
cd "$VULNTRACK_ROOT/backend"
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-dev.lock
python -m pip install --no-deps -e .
python -m pip check
python -m pytest -q tests/unit
```

Los lockfiles del commit son la referencia reproducible; no actualizar versiones por accidente durante recuperación. En Windows la activación de venv difiere según shell. Las 77/122/29 pruebas anteriores son evidencia histórica, no el resultado que tendrá automáticamente esta PC. Registrar resultados nuevos con versión y fecha.

## 5. PostgreSQL y pruebas exclusivamente desechables

El backend requiere referencia V1 exacta para su runner; este verifica hashes y rechaza SQL de V2/audit como referencia. Los directorios se llamaron backend/database, por ello definir explícitamente DATABASE_V1_PATH:

```bash
cd "$VULNTRACK_ROOT/backend"
source .venv/bin/activate
DATABASE_V1_PATH="$VULNTRACK_ROOT/database" python scripts/test_postgres.py -q
```

El runner crea nombre aleatorio propio, tmpfs y puerto efímero solo localhost; instala V1 y migra **su PostgreSQL temporal**, no acepta una URL existente. Se detiene su recurso al finalizar y los datos tmpfs se descartan. Requiere Docker listo y acceso a imagen fijada. No usar `--catalog`, que escribe el manifiesto, al recuperar. No ejecutar Alembic contra una base persistente. La prueba V2 adversarial confirma datos ficticios y solo se ejecuta en un clúster nuevo controlado: leer primero `database-v2-audit/docs/VALIDACION_SEGURIDAD_V2.md`; no copiarla a una instalación operativa. V2 sigue experimental.

## 6. Comprobar arranque real de FastAPI con PostgreSQL temporal

```bash
cd "$VULNTRACK_ROOT/backend"
docker build -t vulntrack-backend:v1.3 .
DATABASE_V1_PATH="$VULNTRACK_ROOT/database" python scripts/test_postgres.py --docker-smoke
```

Ese modo existente del runner verifica servicio real, `/health`, `/ready`, login, activos, replay y CORS usando credenciales temporales generadas, sin reutilizar una base persistente. No supone producción lista ni frontend funcional. No se ejecutó este smoke en la misión de sincronización; los comandos se contrastaron con el script. El runner conserva contenedores propios detenidos; revisar etiquetas/nombres antes de cualquier limpieza, nunca eliminar contenedores ajenos o volúmenes por patrones amplios.

## 7. Variables privadas y desarrollo continuo

Para una futura instancia local independiente: copiar `.env.example` a `.env` **solo en clonación nueva sin .env**, permisos 600 en sistemas POSIX y editar localmente. Nunca copiar la contraseña de ejemplo como secreto operativo ni subir .env. Generar TOKEN_SECRET nuevo localmente con Python secrets y almacenarlo directamente en el archivo/gestor privado, sin enviarlo por chat ni logs. Configurar DATABASE_URL con login de aplicación no dueño/superusuario y contraseña codificada para URL; PostgreSQL debe estar en la revisión de esquema compatible. MIGRATION_DATABASE_URL solo para mantenimiento autorizado, nunca proceso API. Ver README backend y docs/MIGRATIONS.md para el proceso aprobado; V1 original carece de created_by y readiness puede devolver503 si no se preparó una copia compatible.

Database Compose es infraestructura V1 con volumen; si después se autoriza crear desarrollo persistente, usar proyecto nuevo, contraseña privada y revisar puertos/volúmenes antes de iniciar. Cambiar .env no reinicializa volúmenes anteriores. Esta guía prioriza pruebas temporales y no prescribe aplicar V2 ni migraciones destructivas en una base persistente. Frontend no tiene package.json: no ejecutar npm install como si ya hubiera aplicación. React/web y móvil se construirán tras ratificar contratos.

## 8. Continuar el MVP

1. Revisar con Rudi aprobación parcial y detalles técnicos: scope, ownership, version null, tabla de identificación/historial y cobertura. Actualizar registro sin modificar D-001/D-002.
2. Coordinar SQL/bootstrap/grants con ORM/Alembic/catálogo; ambos desarrolladores pueden contribuir a cualquier repositorio, revisión cruzada obligatoria por contrato.
3. Construir vertical registrar→buscar CPE→confirmar→consultar CVE→persistir→mostrar/revisar, con fixtures y autorización por activo; no ZAP/workspaces operativos en primera entrega apoyada por Jesel.
