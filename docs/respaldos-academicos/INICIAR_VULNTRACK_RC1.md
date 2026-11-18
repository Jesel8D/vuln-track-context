> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Abrir VulnTrack RC1 sin cambiar la instalación habitual

La candidata está en `/home/USUARIO/VulnTrack-rc1` y usa sus propios datos.
Abrir **http://127.0.0.1:5177**; Swagger: **http://127.0.0.1:8004/docs**.
5173/8000 siguen perteneciendo a tu instalación habitual.

Si RC1 está detenida, abrir Ubuntu desde PowerShell:

```powershell
wsl -d Ubuntu-24.04
```

Dentro de Ubuntu:

```bash
export PATH="/home/USUARIO/.local/opt/node-v24.18.0-linux-x64/bin:$PATH"
cd /home/USUARIO/VulnTrack-rc1/vuln-track-backend
.venv/bin/python -m scripts.v2_development status --profile rc1
```

Si ya corre, abrir las URLs. Si está detenida, mantener abierta esta terminal:

```bash
.venv/bin/python -m scripts.v2_development up --profile rc1 \
  --pg-port 55438 --api-port 8004 --web-port 5177 \
  --frontend ../vuln-track-frontend --nvd-mode fixture
```

Crear cuenta normal o consultar el acceso ficticio ya preparado únicamente en una
terminal privada, sin proyectar su contraseña:

```bash
.venv/bin/python - <<'PY'
import json
from pathlib import Path
print(json.loads(Path('.local/rc1/demo-access.json').read_text())['primary'])
PY
```

No publicar esa salida. No hace falta volver a ejecutar las pruebas para mostrar
el dataset. Recargar exige login porque el JWT vive en memoria; PostgreSQL
conserva los datos.

Para detener desde otra terminal Ubuntu en el mismo directorio:

```bash
.venv/bin/python -m scripts.v2_development stop --profile rc1
```

El volumen permanece. Para respaldo:

```bash
.venv/bin/python -m scripts.v2_development backup --profile rc1
```

El dump/manifiesto quedan privados bajo `.local/rc1/backups/`. Son sensibles: no
subirlos a Git ni compartirlos en el informe. Restore siempre a **otro perfil y
volumen nuevos**, nunca sobre RC1 o development.

Guía completa de ejecución y recuperación (referencia local no incluida) ·
Guion de demostración de10–15min (referencia local no incluida) ·
[Informe A–T](REPORTE_V2_RC1.md).

NVD fixture visible; sin consultas NVD en vivo, verificación remota, egreso público
ni ZAP. Candidato para revisión local; la aprobación final de Jesel/Rudi está pendiente.
