# Inventario de respaldo y recuperación

Corte de verificación del paquete publicado: 2026-11-08, commit `411a8f253e4a6d1db6f18f3c4ea7a5548c42d996`. Hashes comprobados mediante ls-remote y clonaciones bare independientes por SSH desde GitHub. No equivale a copia íntegra del equipo. Este inventario se actualiza en un commit posterior: no puede contener su propio hash. El hash final de esa actualización se comprueba tras su push y figura en el reporte final; puede resolverse siempre con `git ls-remote origin refs/heads/docs/mvp-context-consolidation`. La tabla siguiente es el corte verificable del paquete, no una afirmación de que su commit de inventario sea el mismo.

| Repositorio | Rama | Commit local o referencia local inspeccionada | Commit remoto observado | Estado | Contenido principal |
|---|---|---|---|---|---|
| Backend | feat/backend-v1-assets | 2cb124d7cc41e22a62a305760826240f9208c257 | 2cb124d7cc41e22a62a305760826240f9208c257 | Ya sincronizado | Backend V1.3 íntegro |
| Backend | rudi | 1a7ec7b6b13612339f14b94850119638660514c6 | 1a7ec7b6b13612339f14b94850119638660514c6 | Ya sincronizado | documentación complementaria |
| Backend | main | b5e71f24ee10793a87b161a3aba3f1b3b19c6d07 | b5e71f24ee10793a87b161a3aba3f1b3b19c6d07 | Ya sincronizado; sin tocar | inicialización histórica |
| Database | feat/postgresql-database-v1 | b493fd17e9290ca94fa085d49230073ed1837e1a | b493fd17e9290ca94fa085d49230073ed1837e1a | Ya sincronizado | V1 SQL/pruebas; cambio local de diagrama separado |
| Database | rudi | 54b7644467a1096c011daf3e5c23d7f9a7773f93 | 54b7644467a1096c011daf3e5c23d7f9a7773f93 | Ya sincronizado | V2 experimental |
| Database | audit/database-v2-security-validation | 2c9723146e910aa51211958556961b4da7019ce3 | 2c9723146e910aa51211958556961b4da7019ce3 | Publicado y verificado | tests corregidos e informe incompleto, sin merge |
| Context | dev/jesel | d481061e5ecb53b9a033086481add37f04d8c549 | d481061e5ecb53b9a033086481add37f04d8c549 | Ya sincronizado; sin tocar | contexto histórico |
| Context | rudi | 6c8baf74a3d7bcd7a0a9ebcca2dda0d797cbfeeb | 6c8baf74a3d7bcd7a0a9ebcca2dda0d797cbfeeb | Ya sincronizado | documentación V2 |
| Context | codex/decision-readiness | f7189656c477c8d33728b8ed8c7587b6b0ec8696 | f7189656c477c8d33728b8ed8c7587b6b0ec8696 | Ya sincronizado | paquete antecedente de decisiones |
| Context | codex/implementation-master-plan | 9a09af4e30ad09036a32c9c283ad48172dfdadad | 9a09af4e30ad09036a32c9c283ad48172dfdadad | Ya sincronizado | plan antecedente |
| Context | docs/mvp-context-consolidation | 411a8f253e4a6d1db6f18f3c4ea7a5548c42d996 | 411a8f253e4a6d1db6f18f3c4ea7a5548c42d996 | Publicado y verificado (corte del paquete, ver nota de autorreferencia) | aprobación individual, contrato,31 informes, recuperación y parche |
| Frontend | main sin commits | no hay HEAD | sin ramas remotas | No aplicable | repositorio vacío, sin aplicación inventada |

Remotos de recuperación SSH: `git@github.com:Jesel8D/vuln-track-backend.git`, `git@github.com:Jesel8D/vuln-track-database.git`, `git@github.com:Jesel8D/vuln-track-context.git`, `git@github.com:Jesel8D/vuln-track-frontend.git`. Origin de Database/Frontend permanece configurado HTTPS en el equipo anterior; SSH apunta al mismo repositorio verificado, no se modificaron URLs ni credenciales globales. Backend y Context ya usaban SSH.

## Carpetas incorporadas

- `investigacion-nvd-cpe`:11 Markdown → `research/nvd-cpe/`.
- `auditoria-integracion`:8 Markdown → `research/architecture-audit/`.
- `consolidacion-mvp`:12 Markdown → `research/mvp-consolidation/`.
- Diff local de `database/diagrams/database_v1.mmd` → `research/local-preservation/database_v1-diagram-uncommitted.patch`, pendiente, sin aplicarlo a Database.
- Canonical derivado del contrato → `docs/VULNTRACK_MVP_CONTRACT.md`; aprobación individual → `docs/MVP_APPROVAL_RECORD.md`; contexto/handoff/setup en docs.

Los originales externos no se borran. Hashes y cambio de enlaces relativos en `research/SOURCE_IMPORT_MANIFEST.md`. La duplicación intencionada del contrato conserva propuesta histórica original y derivación de estado individual actual; no hay dos contratos globalmente aprobados.

## Lo que permanece solo local, no respaldado por estos commits

`material-estudio/` es material pedagógico histórico opcional, no prerrequisito de implementación. No se publicó en esta misión:

| Archivo o grupo local | Tamaño al inventariar | Motivo |
|---|---:|---|
| VulnTrack_Guia_Estudio_Avances_2026-10-30.pdf | 158772 bytes | PDF histórico fuera del paquete necesario de desarrollo |
| guia_estudio.md | 79117 bytes | fuente educativa histórica, contenido técnico sustituido para desarrollo por contrato/fuentes |
| generar_guia.py | 14827 bytes | generador del PDF, no módulo de VulnTrack |
| verificar_fuentes.py | 3404 bytes | verificación de guía histórica |
| verificar_pdf.py | 3227 bytes | verificación editorial histórica |
| verificacion_fuentes.json | 16632 bytes | snapshot de verificación histórica |
| control_calidad.json | 3813 bytes | informe editorial histórico |
| tmp/ y sus PNG/intermedios | variable | caché/renderizado, excluidos |

Si se desea trasladar esos materiales educativos, se requiere copia adicional: **no están respaldados por este inventario remoto**. Las carpetas originales con 31 Markdown siguen locales pero sus contenidos sí se incorporan al contexto con enlaces portátiles. El archivo modificado de diagrama sigue sin commit en Database, aunque su diff exacto se publica en Context y permite reconstrucción con revisión manual.

No se encontraron archivos no rastreados adicionales en los cuatro repositorios al iniciar. No se copiaron .env reales, venv/cachés, claves, credenciales, contenedores ni volúmenes. Preparar variables privadas nuevas en cada equipo; recuperar datos de una base operativa exigiría un procedimiento autorizado distinto, no cubierto aquí. Los temporales y scripts de trabajo en /tmp no forman parte del proyecto recuperable.

## Seguridad y validación

Se revisan archivos nuevos y rama audit antes de push: ausencia de .env privados, claves/tokens detectables, binarios grandes y contenido ajeno; ejemplos públicos de prueba y nombres CPE/UUID no son credenciales reales. Se comprueban `git diff --check`, JSON, enlaces y D-001/D-002 intactas. Ningún escaneo automatizado por sí solo garantiza ausencia universal de secretos; esta publicación se limita a documentación y tests inspeccionados. Ninguna migración o suite operativa se ejecuta durante sincronización.

## Evidencia de recuperación y revisión final

Se clonaron por SSH los repositorios Context, Backend y Database en directorios bare temporales independientes de los repositorios locales, recuperando las ramas de la tabla y comprobando sus hashes. Desde la copia remota de Context se comprobaron11 Markdown NVD,8 de auditoría y12 de consolidación, y se compararon byte a byte con el árbol local el contrato, aprobación, setup, handoff, inventario del corte y parche. Frontend se consultó con ls-remote: sin ramas, no hay contenido que respaldar.

El parche del diagrama se aplicó únicamente a una copia temporal del archivo base b493fd1 y reconstruyó exactamente el archivo local modificado (mismo hash), sin modificar Database. Los árboles de trabajo originales y sus cambios fueron comparados por hashes/estado inicial; la rama de contexto nueva está en worktree separado. No hubo merge, force, rebase, borrado de ramas ni escritura a main.

Worktrees conservados: `/home/mz/Desktop/VulnTrack/database` en V1 y `/home/mz/Desktop/vulntrack-database-v2-audit` en audit; Context original `/home/mz/Desktop/VulnTrack/context-general-ia` en dev/jesel y nuevo `/home/mz/Desktop/vulntrack-context-mvp-sync` en docs/mvp-context-consolidation. Estas rutas son procedencia del equipo anterior; en nueva PC se recuperan mediante clone/worktree de SETUP_NEW_PC, no copiando .git ni enlazando esos directorios. Los temporales bare de verificación no se publican ni se necesitan para continuar.

Resultado de seguridad: no se detectaron .env privados, patrones de claves privadas, tokens GitHub/AWS/JWT literales ni archivos de más de1MB en el paquete; revisión manual de contenido y diff preparado realizada. Rama audit contiene tests sintéticos y guía, no credenciales reales; su commit previo se publica sin reescribirlo. Los ejemplos públicos de contraseñas de desarrollo del baseline no deben utilizarse como credenciales reales. La sincronización no ejecutó pruebas de aplicación, migraciones ni llamadas NVD.
