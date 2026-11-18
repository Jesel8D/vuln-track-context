> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Manifiesto RC1 para revisión académica

**Candidato técnico listo para revisión formal; aprobación académica definitiva:
PENDIENTE.** Este manifiesto complementa la entrega RC1 anterior; no reemplaza ni
reescribe su evidencia. Se crea únicamente documentación/evidencia de revisión.

## Conjunto congelado y verificado

| Repositorio | Rama de referencia | Hash completo |
|---|---|---|
| Backend | release/v2-rc1-integration | `4453ae0d9b25de361947113f7c61c1b4797385ab` |
| Frontend | release/v2-rc1-web | `4674cbcd9f46ad0e9e587b0db630b9f51a990a86` |
| Database | feat/v2-security-verification-database | `a562e638dfe5b7e8d07eec0e8046e6998f8a2802` |
| Context | docs/mvp-context-consolidation | `caa7ef13b0e2b24848991e32b3c0ee3fc59139c7` |

Los cuatro HEAD locales coinciden con las ramas remotas inspeccionadas ahora.
Worktrees RC1 y ocho checkouts originales Windows/WSL limpios. No se hace merge,
checkout a otra versión, commit/push, migración, tag o cambio de datos de
inventario. Las únicas sesiones nuevas son logins sobre cuentas ficticias RC1
para lectura. Prueba de referencias (referencia local no incluida).

OpenAPI publicado y actual iguales: versión0.2.0, 44 operaciones; SHA-256 del
archivo versionado:
`082d1c6a0b84564b8814ede1724b5d72e54249d5fd9893d65149b0af00133b16`.
Postman histórico63315644121bb0074cd18f5d2da11720358caea0 ya es ancestro de la
base RC1; no se necesita fusionarlo nuevamente. V1 análisis responde 202, V2
responde 201 según sus contratos actuales.

## Evidencia heredada de la misión de construcción RC1 (no reejecutada ahora)

| Grupo | Resultado registrado | Precisión de interpretación |
|---|---|---|
| Backend existing / new | 755/755 aprobadas; 0 fallidas/omitidas por ruta | Misma suite en dos bootstraps, no 1510 casos únicos;734 anteriores + 21 nuevas |
| SQL | 120 comprobaciones por ruta y 27 llamadas adversariales adicionales | Bases tmpfs propias; no development |
| Frontend | 96 unitarias/componentes, 8 archivos; TypeScript, ESLint y build correctos | Evidencia del commit RC1, no ejecución nueva de esta revisión |
| Navegador previo V2 | 6 aprobadas por existing y 6 por new | Misma suite repetida |
| Navegador RC1 | 9 aprobadas  + 1 tras reinicio  + 1 tras restore; repetición UX 4 | Casos repetidos no suman nuevas funcionalidades; cuatro anchos con axe |
| Newman | 283 aserciones/74 HTTP existing; 288/75 new; 185/50 contrato persistente | 0 fallidas; polling cambia conteos; contrato persistente sobre tmpfs propia |
| Recuperación | 13 comprobaciones aprobadas | Reinicio, dump privado, restore a volumen nuevo y aislamiento |
| Laboratorios 2B/2C | 45 y 51 comprobaciones aprobadas | Solo redes/fixtures propios; no certifican egreso público |

Fuente: resultados saneados de RC1 (referencia local no incluida),
recuperación (referencia local no incluida) y
manifiesto previo (referencia local no incluida). Los hashes de sus 24 archivos
exportados se validaron en esta revisión. No se publican logs brutos ni secretos.

El backup previamente ensayado es **sensible**, no adjunto: SHA-256
`bd4816ed5b5029d766ad0fa79224fb75ad719029f658e7e3a452c08971feb88e`.
Restore probado en volumen `vulntrack_v2_rc1_restore_49bec2c1_pgdata`, retenido y
detenido al entregar RC1. No se restauró de nuevo en esta misión.

## Evidencia nueva de esta revisión

- **11 comprobaciones** de integridad de entrega, commits, checkouts originales,
  volúmenes/puertos, salud HTTP, configuración, PID/entorno fixture, fábrica
  MockTransport y contrato vivo. Resultado (referencia local no incluida).
- **9 comprobaciones** de navegador/API de lectura: login normal, procedencia,
  datos/CPE persistentes, CVE revisado/recomendación, dashboard real, búsqueda
  capturada, lectura ajena 404 / propia 200, historial, desafío expirado y Baseline
  disabled, con cero orígenes externos observados. Las comprobaciones agrupan
  varias aserciones; no son 20 pruebas pytest ni una suite completa nueva.
  Resultado (referencia local no incluida).
- Cuatro referencias remotas exactas verificadas; propuesta de tag ausente
  local/remota en Backend/Frontend. Ningún tag o push ejecutado.
- Revisión de S1/S2/S3 y código V1/V2;38AC trazados como cobertura/evidencia y
  brechas, sin asignar aprobaciones individuales.

En el navegador se bloquearon todos los orígenes salvo web5177/API8004. Además,
la fábrica activa de API conecta CPE, CVE y worker a MockTransport con AND
original. Esto acredita el recorrido NVD fixture observado; **no afirma que
Windows/WSL carezcan de toda conectividad Internet** ni que exista un perímetro
de egreso público operativo. La verificación remota sigue bloqueada.

## Entornos y limitaciones

| Recurso | RC1 | Personal protegida |
|---|---|---|
| Directorio | /home/USUARIO/VulnTrack-rc1 | /home/USUARIO/VulnTrack |
| Web/API/PG |5177/8004/55438 |5173/8000/55434 |
| Volumen | vulntrack_v2_rc1_pgdata | vulntrack_v2_development_pgdata |
| PostgreSQL | Contenedor propio sano, puerto 127.0.0.1 | Contenedor original sano, puerto 127.0.0.1 |

Claves/credenciales separadas; Vite recibe solo URL/modo públicos y ninguna
credencial Backend. Configuración/dumps/accesos privados no se copian. El dataset
es ficticio: CVE-2017-6784 con AND, `undetermined`, `reviewed`, recomendación
`pending`; cero abiertos no significa seguro. JWT en memoria, recarga exige login,
logout no revoca individualmente tokens servidor. Sin móvil nativo, sin UI de
dispositivos montada, sin gestión pública de membresías ni historial CPE V2
equivalente a V1. Ver [registro completo](05_DEFECTOS_RIESGOS.md).

ZAP, verificación remota, egreso público del ejecutor y despliegue Internet siguen
bloqueados. No hubo consultas NVD live, sondeos de URL objetivo, cambios al
firewall global/daemon ni migraciones destructivas. No se declara producción lista.

## Propuesta de etiqueta Git (NO EJECUTADA)

Nombre propuesto: **`v0.2.0-rc1`**, etiqueta anotada de candidato académico. V2 es
el nombre de la línea de producto; no se inventa por ello una versión API2.0.0.
El nombre propuesto estaba libre local/remoto al verificarlo. Antes de usarlo,
ambos deben acordar si identifica solo al candidato técnico o una aceptación
académica, revisar de nuevo su disponibilidad y documentar la decisión.

| Repositorio | Destino propuesto, sin crear referencia |
|---|---|
| Backend |4453ae0d9b25de361947113f7c61c1b4797385ab |
| Frontend |4674cbcd9f46ad0e9e587b0db630b9f51a990a86 |

Database y Context se fijan por hash en el manifiesto, sin tags nuevos propuestos
necesarios en esta sesión. La anotación debería mencionar los cuatro hashes,
fixture, bloqueos y estado de aceptación; un tag en cada repo no es una transacción
multirrepo atómica. No se han ejecutado `git tag` de creación ni pushes de tags.
No usar ni mover una etiqueta existente mediante force.

## Integridad del paquete

manifiesto_rc1.json (referencia local no incluida) contiene hashes de fuentes/evidencias y
documentos nuevos, resultados reutilizados/nuevos separados y estados humanos
PENDIENTES. SHA256SUMS.txt (referencia local no incluida) permite comprobar también el manifiesto;
no se incluye un hash circular del propio archivo de sumas. Las versiones
originales del reporte RC1 y de Context permanecen intactas.
