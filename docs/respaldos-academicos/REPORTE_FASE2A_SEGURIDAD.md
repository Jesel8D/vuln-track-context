> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 — entrega de Fase 2A

**IMPLEMENTADO CON LIMITACIONES. ZAP NO está autorizado para activarse.**

Se publicaron las dos ramas nuevas con Jesel8D mediante push normal y se
comprobaron sus hashes remotos. No se crearon PR ni merges, ni se desplegaron
estos cambios sobre la instalación personal. No se modificaron main o rudi.

## Informe solicitado

| Apartado | Clasificación | Resultado |
|---|---|---|
| 1. Código inspeccionado | IMPLEMENTADO Y PROBADO | Cuatro repositorios, ramas, remotos, worktrees, procesos, Docker, SQL007, API/JWT/RLS, worker y React; commits remotos de Fase 1 comprobados antes de intervenir |
| 2. Protección de datos | IMPLEMENTADO Y PROBADO | Principal conservado en /home/USUARIO/VulnTrack; sin parada, cambio de versión, restore, pruebas contra cuentas personales o modificaciones del volumen protegido |
| 3. Controles nuevos | IMPLEMENTADO CON LIMITACIONES | Transporte HTTPS fijado a IP y referencia local de desafíos con propósito único, hash, caducidad, consumo atómico, revocación y auditoría; sin conexión al servicio real |
| 4. Pruebas | IMPLEMENTADO Y PROBADO | Backend 598 por recorrido; React 86; navegador 6 por recorrido; SQL 96 por bootstrap + 27 adversariales; Newman 257/267/177 aserciones; 27 comprobaciones del laboratorio real |
| 5. Limitaciones | PENDIENTE | Persistencia/RLS de pruebas de control, resolver DNS operativo acotado, integración del servicio, egreso público selectivo y operación duradera de futuros jobs |
| 6. Control de dominio | IMPLEMENTADO CON LIMITACIONES | Emisión y comprobación por HTTPS real probadas solo con dominio/IP simulados en el laboratorio. Verificación de dominios reales BLOQUEADA; declaraciones anteriores siguen sin verificar |
| 7. Transporte SSRF | IMPLEMENTADO CON LIMITACIONES | TCP/TLS IPv4/IPv6, IP fijada y Host/SNI/certificado originales probados; sin resolución silenciosa, proxies, redirects o retries. No está conectado a API/worker; DNS operativo pendiente |
| 8. Aislamiento de red | IMPLEMENTADO CON LIMITACIONES | Namespace network-none, reglas IPv4/IPv6 independientes de Python y sondas TCP/UDP probadas con canarios locales; no demuestra egreso seguro a Internet de una plataforma futura |
| 9. Frontend | IMPLEMENTADO Y PROBADO | Distingue declaración, verificación y elegibilidad con estados API reales; desktop/móvil, contrato y accesibilidad ensayados; botón siempre deshabilitado |
| 10. ZAP | BLOQUEADO | No instalado ni ejecutado, sin endpoint habilitado ni escaneos externos o resultados ficticios |
| 11. Ramas y commits | IMPLEMENTADO Y PROBADO | Backend 7198de7 y Frontend 1a0263a publicados; hashes local/remoto coincidentes y worktrees limpios |
| 12. Actualización y reversión | IMPLEMENTADO CON LIMITACIONES | Procedimiento concreto y ensayo estable → Fase 1 → Fase 2A → estable; reinicio, backup y restore en volúmenes nuevos. Ejecución manual sobre el principal PENDIENTE |
| 13. Antes de ZAP | PENDIENTE | Checklist independiente de controles implementados, probados, pendientes y decisiones de Jesel/Rudi; autorización posterior explícita obligatoria |

## Publicación

| Repositorio | Rama | Commit completo |
|---|---|---|
| Backend | feat/v2-security-analysis-phase2a | 7198de7c986f6b86a0f2ac1a94d1d88b893b3a84 |
| Frontend | feat/v2-security-analysis-web-phase2a | 1a0263a859ecdbf844887d27ae17c7c6fa903ed7 |

- [Commit Backend](https://github.com/Jesel8D/vuln-track-backend/commit/7198de7c986f6b86a0f2ac1a94d1d88b893b3a84).
- [Commit Frontend](https://github.com/Jesel8D/vuln-track-frontend/commit/1a0263a859ecdbf844887d27ae17c7c6fa903ed7).

Worktrees nuevos en /home/USUARIO/VulnTrack-security-phase2a. Database sigue en
b377b5b y Context en caa7ef1, sin cambios ni ramas nuevas. No se modificaron
SQL, migraciones, contratos HTTP existentes ni archivos lock.

## Qué se comprobó realmente

Las 59 pruebas unitarias nuevas de Fase 2A están incluidas en las 598 del Backend,
no se suman aparte. Ambos recorridos existing/new terminaron sin fallos ni
pruebas omitidas. SQL001/002/004/005/006 aportaron 55+21+6+10+4 comprobaciones por
bootstrap; SQL003 se ejecutó en otra base tmpfs y pasó 27.

La colección conserva 61 solicitudes definidas. Newman envió 67, 69 y 47
solicitudes respectivamente por polling y guardias, con 257, 267 y 177 aserciones
aprobadas y cero fallos. La modalidad persistent-contract se probó sobre tmpfs,
nunca contra la base personal. No se suman repeticiones como casos únicos.

OpenAPI conserva 39 operaciones, 17 cuerpos Pydantic y 42 ejemplos positivos.
Su SHA-256 permanece 41dc15ba8c53292d15e78a6d6c87a1cf1e8956c94dbffae5a6cf6e9275b09d97.
TypeScript, ESLint, compilación, Ruff y pip check pasaron. Los avisos heredados
de Starlette/Newman se conservaron sin actualizar dependencias.

El laboratorio hizo conexiones TCP/TLS reales IPv4/IPv6 con getaddrinfo
prohibido durante la conexión. Comprobó Host y SNI, certificado válido, hostname
incorrecto, CA no confiable, certificado expirado, límites, redirecciones y
cancelación. Verificó un desafío aleatorio de propósito único por HTTPS del
fixture y su revocación. Los tokens no se publican en logs o reportes.

Se verificó que solo existiera loopback, sin rutas de salida. Sondas que saltan
Python fueron bloqueadas por netfilter y los canarios locales no recibieron
tráfico. Worker UID 65532, capacidades cero, no-new-privileges, raíz de solo
lectura, sin bind mounts del usuario, Docker socket o secretos de aplicación;
cgroups de CPU/memoria/PIDs comprobados y OOM real en una sonda propia de 32 MiB.
No hubo fugas de descriptores de socket ni quedaron contenedores de ese laboratorio activos.

Esto prueba un laboratorio sin salida. No prueba todavía un runner con egreso
público, DNS autorizado real, scheduler, aislamiento entre jobs o recuperación
operativa tras caídas. El helper NET_ADMIN fue efímero, de confianza y limitado
al namespace propio; el worker nunca obtuvo esa capacidad.

## Persistencia y protección

El volumen principal vulntrack_v2_development_pgdata se conservó. PostgreSQL
sigue ligado a 127.0.0.1:55434. Se volvió a verificar el checksum del backup
personal previo, de 155478 bytes, sin mostrar su contenido ni restaurarlo.

El ensayo de versiones utilizó únicamente los volúmenes nuevos
vulntrack_v2_upgrade_76bb1648a3_pgdata y
vulntrack_v2_upgrade_restore_76bb1648a3_pgdata. Se conservaron y sus servicios
quedaron detenidos. Cuenta sintética, membresía, aplicación, destino y versión
de declaración sobrevivieron los cambios de código, reinicio y restore separado;
el otro workspace siguió sin acceso.

La instalación personal sigue ejecutando Backend fcabbe9 y Frontend 4514b68.
El reinicio/actualización manual de esa instalación sigue pendiente de una
ventana acordada, no ocurre como consecuencia del push.

## Cómo continuar sin cambiar la instalación personal

Para revisar Fase 2A en un perfil vacío independiente desde Ubuntu:

```bash
export PATH="/home/USUARIO/.local/opt/node-v24.18.0-linux-x64/bin:$PATH"
cd /home/USUARIO/VulnTrack-security-phase2a/vuln-track-backend
.venv/bin/python -m scripts.v2_development up --profile phase2a-preview --pg-port 55436 --api-port 8002 --web-port 5175 --frontend ../vuln-track-frontend --nvd-mode fixture
```

Frontend de esa vista previa: http://127.0.0.1:5175. API:
http://127.0.0.1:8002/docs. Este perfil NO contiene los datos de tu cuenta
personal y no se inició automáticamente. Si un puerto está ocupado, elegir
otro libre; no detener servicios ajenos.

Para adoptar Fase 1 sobre el principal, el procedimiento prepara backup
verificado, parada en ventana sin actividad, cambio emparejado de ambos commits,
revisión de dependencias y comprobaciones de login/workspaces/destinos. La
reversión vuelve a los dos commits originales con el mismo volumen y claves,
sin restore que pudiera perder datos nuevos. Los comandos exactos y condiciones
para abortar están en el plan enlazado debajo.

## Documentación entregada

- Informe técnico y reproducción (referencia local no incluida).
- Plan exacto de actualización y reversión (referencia local no incluida).
- Checklist independiente antes de cualquier fase ZAP (referencia local no incluida).
- Laboratorio y límites de WSL2/Docker Desktop (referencia local no incluida).
- Contrato y estados del frontend (referencia local no incluida).

Los secretos, backups, logs y capturas quedan en ubicaciones privadas ignoradas
por Git. No se incluyeron en los commits ni en estas copias documentales.
