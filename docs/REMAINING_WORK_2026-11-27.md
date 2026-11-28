# Pendientes de VulnTrack por repositorio — 27-11-2026

Este registro separa el trabajo pendiente de los controles ya observados. La auditoría no equivale a un pentest ni a una aprobación de producción. Los cuatro repositorios tienen rama predeterminada `main`; el inventario se verificó localmente y estaban limpios al iniciar esta actualización.

## Ya completado

- Los cuatro repositorios tienen `logo.png` y `preview.png` en la raíz.
- Los cuatro repositorios tienen licencia MIT y `.gitignore`.
- El README de Backend describe arquitectura, persistencia, endpoints, NVD, seguridad, instalación y límites.
- Backend corrige la lectura de esquemas JSON NVD en Windows, rechaza temprano cuerpos HTTP grandes y limita procesos en Compose; Frontend anuncia accesiblemente errores de confirmación.
- PostgreSQL y la imagen de compilación Node se actualizaron a tags/digests fijos.
- La consulta real de CVE a NVD se conservó y se confirmó con una petición GET de solo lectura. Instalaciones nuevas mantienen el modo live explícitamente deshabilitado; la activación requiere configurar backend y compilación web.
- D-003 registra el alcance autorizado de solo web. No se creará una aplicación móvil.

## Backend

- **NVD live de extremo a extremo:** la ruta real del backend respondió a NVD con un CPE de prueba, pero falta verificar el botón de la interfaz contra un backend local configurado en live. La plantilla de producción sigue deshabilitada por defecto.
- **Egress:** la API está conectada a una red de salida ordinaria. Falta aplicar y validar en el host un proxy/firewall con destinos permitidos, incluidos IPv4, IPv6 y fallo cerrado. Hasta entonces, no habilitar consultas live en producción.
- **Imágenes:** las nuevas imágenes Postgres/Node aún no tienen un reporte actualizado de vulnerabilidades. El escaneo de noviembre previo a la actualización halló avisos que deben reevaluarse con las imágenes finales.
- **Sesiones:** JWT no tiene revocación individual; el TTL corto limita la exposición, pero logout solo elimina la copia del navegador.
- **Abuso:** login/registro y cuotas viven en memoria por proceso. Antes de varias réplicas, trasladar cuotas a un servicio compartido o al perímetro; para la exposición pública, evaluar verificación y límites duraderos.
- **Cobertura de pruebas:** 260 pruebas seleccionadas pasaron en la revisión previa; 5 pruebas de laboratorio se excluyeron por dependencias Unix. Se corrigió el lock/proceso local del launcher Windows y pasó una prueba aislada del lock y `py_compile`. La suite completa sigue pendiente: en este entorno `pytest` no pudo cargar `tests/conftest.py` porque falta `argon2-cffi`; repetirla en un entorno con dependencias fijadas y CI Linux.

## Frontend

- **CVE live en interfaz:** el frontend contiene el modo `VITE_NVD_MODE=live`; falta demostrar una ejecución completa con CPE confirmado, backend live, petición real y hallazgo/estado persistido. La configuración normal usa `disabled` o fixture.
- **Datos de demostración:** los resúmenes CVE y gráficas del dashboard no equivalen a resultados live. Mantenerlos rotulados y documentar qué vistas usan datos reales antes de la entrega.
- **Prueba visual/E2E:** `npm run check` pasó (181 pruebas, tipos, lint y build); falta el recorrido E2E del CVE live con servicios aislados.
- **Móvil:** fuera del alcance por D-003.

## Database

- **PostgreSQL actual:** Compose fija 16.16; la suite reportada se ejecutó con 16.15. Repetir `tests/run.sh` con un proyecto y volumen nuevos, sin tocar la instancia personal.
- **Migraciones:** probar desde cero y la ruta aditiva V1→V2 con datos sintéticos y respaldo restaurable. Los informes actuales señalan que no se validaron todas las migraciones posteriores ni restauración/rollback completos.
- **RLS/HMAC:** terminar pruebas de ciclo de vida, replay/nonce, provisión/rotación de clave, roles y permisos sobre todas las entidades. Los resultados históricos de V2 son incompletos y no sustituyen integración con la API actual.
- **V1:** la separación por propietario depende de filtros de API; no entregar credenciales SQL V1 a clientes ni habilitar consultas directas para usuarios.
- **Documentación histórica:** `TEST_RESULTS.md`, `PROVISIONAL_DECISIONS.md` y otros documentos conservan resultados/versiones de octubre/noviembre anteriores. Deben seguir marcados como evidencia histórica; actualizar al repetir las pruebas, no sobrescribir resultados antiguos.

## Context

- **Historial documental:** `PROJECT_MASTER_CONTEXT.md` y `REPOSITORY_READINESS.md` tenían un diagnóstico de repos vacíos y se reemplazaron/etiquetaron con el estado vigente. Muchos planes y criterios respaldados aún mencionan flujos móviles; conservarlos como historia, enlazar D-003 y no presentarlos como requisitos vigentes.
- **Aprobación:** la ratificación técnica del MVP por el segundo desarrollador sigue pendiente según el registro de aprobación del 2026-11-08. D-003 registra únicamente la instrucción explícita web-only del usuario solicitante.
- **About de GitHub:** descripciones y topics están preparados en [GITHUB_ABOUT.md](GITHUB_ABOUT.md); falta que el dueño los guarde en la sección lateral porque la sesión actual carece de administración.
- **Commits:** la historia local de `main` en Backend y Frontend ya atribuye a `RudiFabricio8` los commits cuyo autor/committer era Codex. Los tags `pre-rudi-author-rewrite-20261127` conservan los originales, y se comprobó igualdad exacta del árbol de archivos. Las ramas de trabajo y las identidades ajenas no se cambiaron. Falta publicar los tags y actualizar `main` remoto; GitHub no respondió al fetch (conexión a `github.com:443` no disponible en esta sesión).

## Bloqueos comunes de producción y seguridad

- El despliegue sigue **NO-GO público** hasta revisar host, firewall, TLS, monitorización, secretos, respaldos cifrados y una restauración ensayada.
- No se ejecutó DoS/DDoS ni carga contra servicios compartidos. No lanzar flood a producción. La siguiente prueba apropiada es carga acotada en staging autorizado con umbrales, parada automática y monitorización.
- No se enviaron payloads SQLi/XSS a una instancia viva porque la aplicación/DB no estaba levantada. Usar pruebas automatizadas locales y staging aislado; nunca probar contra activos sin autorización explícita.
- Docker daemon no estaba disponible en esta revisión: `docker info` no pudo conectar al engine. Las validaciones que necesiten PostgreSQL/Docker siguen pendientes hasta que un daemon local esté operativo. No borrar volúmenes para desbloquear pruebas.
- La auditoría no certifica ausencia de vulnerabilidades ni garantiza una calificación docente; no se proporcionó rúbrica.
