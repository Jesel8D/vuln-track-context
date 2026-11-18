> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 — resultado de Fase 1

**IMPLEMENTADO CON LIMITACIONES. ZAP NO está autorizado para activarse.**

La implementación y las validaciones están terminadas en worktrees separados.
Las dos ramas nuevas se publicaron mediante push normal con Jesel8D y se
verificó que sus hashes remotos coinciden con los locales. Ambos worktrees están limpios.

La instalación personal continúa activa en /home/USUARIO/VulnTrack, con sus ramas
estables originales. Los cambios nuevos no se desplegaron sobre esa sesión.
No se creó PR ni se hicieron merges, force push, cambios de locks o migraciones
experimentales sobre la base personal.

| Apartado | Clasificación | Resultado |
|---|---|---|
| A. Estado inicial | IMPLEMENTADO Y PROBADO | Cuatro repositorios limpios y referencias remotas verificadas; bases fcabbe9/b377b5b/4514b68/caa7ef1; rama Postman 6331564 inspeccionada sin merge |
| B. Persistencia | IMPLEMENTADO CON LIMITACIONES | Prueba completa en volúmenes aislados con cuenta, membresía, aplicación, evidencia e historial conservados y aislamiento A/B; reinicio manual de la instalación personal pendiente |
| C. Backup/restore | IMPLEMENTADO Y PROBADO | Backup principal generado y checksum verificado; restauración de datos sintéticos en otro volumen, sin sobrescribir el principal |
| D. Auditoría | IMPLEMENTADO CON LIMITACIONES | approved es declaración, no control del dominio; auditoría de altas/cambios y retiradas; verificación remota y almacén de evidencia pendientes |
| E. Backend | IMPLEMENTADO Y PROBADO | Validación de destinos públicos, canonicalización, metadata analysis_readiness, retirada versionada y auditada, errores sin eco de credenciales |
| F. Database | IMPLEMENTADO Y PROBADO | Compatibilidad con SQL007, sin cambios SQL, modelos o migraciones nuevos; no se creó rama Database |
| G. Frontend | IMPLEMENTADO Y PROBADO | Estados respaldados por API, retirada de declaración, validación del contrato, botón siempre deshabilitado, contraste y accesibilidad probados en escritorio/móvil |
| H. SSRF | IMPLEMENTADO CON LIMITACIONES | Política local y planes/revalidación DNS con resolutores simulados; transporte con IP fijada y red independiente todavía pendientes |
| I. Pruebas nuevas | IMPLEMENTADO Y PROBADO | 138 unitarias y 21 de integración Backend incluidas en las suites, más pruebas React y navegador |
| J. Regresiones | IMPLEMENTADO Y PROBADO | Backend 539 por recorrido existing/new; React 82; Chromium 6 por recorrido; SQL 96 por bootstrap y SQL003 27; Postman 232/232/137 aserciones, sin fallos |
| K. RLS | IMPLEMENTADO Y PROBADO | JWT, roles, IDs ajenos, transacciones, concurrencia, rollback y reutilización de conexiones; RLS y privilegios conservados |
| L. Módulo web | IMPLEMENTADO CON LIMITACIONES | Preparación e información reales; sin executor ni historial ZAP ficticio |
| M. ZAP | BLOQUEADO | Sin endpoint ejecutable, sin activación por configuración, botón disabled; no se ejecutó ZAP ni hubo tráfico de escaneo |
| N. Riesgos residuales | PENDIENTE | Prueba remota de control, transporte, egreso independiente, cancelación/revocación durante ejecución, retención y operación de respaldos externos |
| O. Publicación | IMPLEMENTADO Y PROBADO | Dos ramas publicadas con hashes local/remoto coincidentes; sin PR, merge ni force push |
| P. Fase 2 | PENDIENTE | Decisiones de Jesel/Rudi y autorización específica antes de implementar y probar un executor |
| Q. Uso actual | IMPLEMENTADO Y PROBADO | Principal saludable y accesible en 127.0.0.1:5173 y 127.0.0.1:8000; NVD fixture y ZAP deshabilitado |

## Ramas y commits publicados

| Repositorio | Rama | Commit |
|---|---|---|
| Backend | feat/v2-security-analysis-phase1 | 56c1274fdb4f48f7c2cb852c4975c8c0ec5bafff |
| Frontend | feat/v2-security-analysis-web-phase1 | e0f3abbf352b1c23db1654afed3071437b3f2e33 |

- [Commit Backend](https://github.com/Jesel8D/vuln-track-backend/commit/56c1274fdb4f48f7c2cb852c4975c8c0ec5bafff).
- [Commit Frontend](https://github.com/Jesel8D/vuln-track-frontend/commit/e0f3abbf352b1c23db1654afed3071437b3f2e33).

Worktrees: /home/USUARIO/VulnTrack-security-phase1/vuln-track-backend y
/home/USUARIO/VulnTrack-security-phase1/vuln-track-frontend. Database permanece en
b377b5b y Context en caa7ef1, sin commits nuevos.

Las pruebas repetidas y el polling no se suman como casos únicos. El recorrido
Postman existente y nuevo tuvieron 61 solicitudes definidas y 62 envíos cada uno;
la guardia persistente envió 39 solicitudes sobre tmpfs, nunca sobre la base personal.
No se afirma preparación para producción ni protección de conexiones reales que
todavía no existen. Los avisos heredados de Starlette/TestClient y Newman se
conservaron sin actualizar dependencias.

## Continuar usando la instalación personal

La aplicación ya está activa. Para un arranque posterior desde Ubuntu:

```bash
export PATH="/home/USUARIO/.local/opt/node-v24.18.0-linux-x64/bin:$PATH"
cd /home/USUARIO/VulnTrack/vuln-track-backend
.venv/bin/python -m scripts.v2_development up --frontend ../vuln-track-frontend --nvd-mode fixture
```

Desde esa carpeta, estas operaciones son independientes:

| Operación | Comando |
|---|---|
| Consultar estado | .venv/bin/python -m scripts.v2_development status |
| Detener en una ventana sin actividad | .venv/bin/python -m scripts.v2_development stop |
| Respaldar | .venv/bin/python -m scripts.v2_development backup |

No usar down -v ni borrar el volumen vulntrack_v2_development_pgdata. El backup
inicia PostgreSQL si estaba detenido. Para comprobar el reinicio personal,
crear una aplicación DEMO con una cuenta de prueba, anotar IDs y confirmar
pertenencia/aislamiento tras una parada acordada; no borrar cuentas existentes.

## Documentación y reproducción

- Informe técnico y comandos de reproducción (referencia local no incluida).
- Modelo de autorización, estados y controles pendientes (referencia local no incluida).
- Política SSRF, amenazas y requisitos de aislamiento (referencia local no incluida).

Los respaldos, secretos, logs y capturas de pruebas permanecen privados e
ignorados por Git. No se incluyeron en los commits ni en estas copias del informe.
