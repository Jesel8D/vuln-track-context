> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Registro de defectos, brechas y riesgos

**PROPUESTA de severidad para aceptación académica.** No asigna CVSS ni presume
vulnerabilidades nuevas. Se distingue un comportamiento incorrecto reproducido,
una función ausente respecto de un requisito y un riesgo operativo. No corregir
un bloqueo deliberado de seguridad como si fuera un defecto.

## Escala propuesta

| Severidad | Criterio |
|---|---|
| S1 — crítica | Pérdida de datos, fuga entre usuarios o ejecución externa no autorizada; detener aceptación del alcance afectado |
| S2 — alta | Impide un requisito obligatorio central o induce una conclusión materialmente incorrecta; bloquear aprobación definitiva hasta corrección o cambio de alcance explícito |
| S3 — media | Uso dificultado o información incompleta con alternativa segura; documentar y acordar tratamiento |
| S4 — baja | Presentación o documentación menor sin afectar integridad/seguridad del flujo |

La regla «cero S1/S2 abiertos en el alcance acordado» es una recomendación para
AA-D08, no un criterio firmado de antemano. Una brecha S2 puede resolverse con un
cambio contractual explícito; un defecto de seguridad no se resuelve ocultándolo.

## Defectos reales ya corregidos en los commits RC1

Fuente: informe de integración y regresión de la misión RC1 anterior. La
revisión actual comprobó el código y algunas vistas de lectura; no repitió cada
reproducción original. «Corregido» es estado técnico, no aceptación de los revisores.

| ID | Reproducción original / efecto | Severidad propuesta | Corrección y evidencia | Estado |
|---|---|---|---|---|
| DEF-01 | Arrancar en fixture sin procedencia visible podía presentar capturas como consulta actual | S2 | Flag público y FixtureNotice; A/B y aviso visible ahora | Corregido RC1 |
| DEF-02 | Volver de inventario/historial dejaba contadores previos en dashboard | S2 | Refetch al montar; test compara cuatro contadores con API; lectura actual 1/1/0/0 | Corregido RC1 |
| DEF-03 | Mes UTC se mostraba como mes anterior en México | S3 | Formato de período con timeZone UTC, regresión en America/Mexico_City | Corregido RC1 |
| DEF-04 | Texto pequeño/estados con contraste inferior a axe | S3 | Tonos ajustados; cuatro anchos y axe en RC1, sin reglas omitidas | Corregido RC1 |
| DEF-05 | Login móvil no tenía h1 visible/landmark adecuado | S3 | Formulario h1 y presentación aside rotulada; pruebas login/registro | Corregido RC1 |
| DEF-06 | Salto de encabezado CVE y landmarks indistinguibles | S3 | h3 CVE y nombre de sidebar; axe con datos poblados | Corregido RC1 |
| DEF-07 | Nombre largo de workspace en 768 px desbordaba y competía con botón | S3 | Track acotado, wrap y división de título; comprobación scrollWidth/capturas | Corregido RC1 |
| DEF-08 | Reinicio inmediato fallaba al confundir TIME_WAIT con listener activo | S2 | SO_REUSEADDR en sonda, dos pruebas TCP y recovery completo | Corregido RC1 |

**Nuevos defectos funcionales reproducidos en esta revisión de lectura: ninguno.**
No significa ausencia universal de defectos: no se reejecutaron las suites
completas, otros navegadores, móvil, escaneos o pruebas NVD live. No hay una nueva
certificación de seguridad por consultar datos existentes.

## Brechas reales respecto del contrato académico

| ID | Evidencia reproducible / fuente | Severidad de impacto | Condición que bloquea versión definitiva | Tratamiento propuesto |
|---|---|---|---|---|
| GAP-01 | S1/S2 exigen dispositivos; `src/app/App.tsx` solo monta rutas V2 | S2 de alcance | Dispositivos siguen siendo funcionalidad obligatoria por UI | AA-D01/D02: aprobar piloto V2 reducido o entregar cliente V1 separado y probado |
| GAP-02 | No cliente móvil demostrado; AC-F08 original exige móvil/web | S2 de alcance | Móvil no se ha diferido expresamente por ambos | AA-D04: implementación posterior o recorte explícito; responsive no sustituye app nativa |
| GAP-03 | MVP-009 apoyado por Jesel excluía workspaces operativos; RC1 depende de ellos | S2 de alcance | No hay política conjunta de workspaces para la entrega | AA-D03; no apagar RLS ni quitar el selector para fingir propiedad V1 |
| GAP-04 | V1 tiene eventos CPE versionados; V2 actualiza selected_cpe y registra acción de auditoría, sin API/UI pública equivalente de ese historial | S2 si RF-04 completo es obligatorio; S3 para piloto limitado | Se exige historial detallado de identificaciones de dispositivos en esta UI | AA-D06; no equiparar snapshots de análisis o audit_event con todo el contrato V1 |
| GAP-05 | ZAP en requisitos históricos, aplazado individualmente, sin aprobación conjunta | S2 de alcance si ZAP es obligatorio | Se pretende declarar cumplido el alcance histórico completo | Ratificar aplazamiento AA-D05 o reconocer entrega definitiva pendiente; nunca activarlo aquí |
| GAP-06 | Todos los campos de aprobación conjunta siguen pendientes | S2 de gobernanza | Se quiere declarar versión académica definitiva aceptada | Cerrar decisiones y emitir resultados separados Jesel/Rudi, con hashes y alcance |

Estas brechas no son evidencia de una fuga de datos, ni se solucionan inventando
aprobaciones. V1 y V2 pueden coexistir como implementaciones distintas sin que
eso haga completa automáticamente la experiencia de usuario del MVP original.

## Riesgos que deben aceptar o tratar

| ID | Riesgo / evidencia | Impacto | Mitigación o puerta propuesta |
|---|---|---|---|
| R-01 | Confundir CPE/CVE capturado con vulnerabilidad de example.com | Alto, conclusión académica incorrecta | Aviso, guion y AND original; mantener undetermined, no consultar la URL |
| R-02 | Ocultar la falta de móvil o dispositivos bajo el nombre V2 | Alto, incumplimiento de rúbrica | Matriz, decisiones AA-D01/D02/D04 y exclusiones firmadas |
| R-03 | Interpretar declaración/lab como control técnico de dominio | Alto, ejecución futura insegura | verify no operativo, intentos 0 en dataset; egreso/ZAP bloqueados; misión futura independiente |
| R-04 | Exponer dump/configuración o perder el único disco | Alto, secretos/pérdida de recuperación | Dump sensible privado; no publicar; copia externa cifrada/retención como requisito operativo futuro |
| R-05 | Recarga exige login y logout no revoca un JWT servidor ya emitido | Medio para entorno local; mayor si se desplegara | Comunicar política actual y decidir sesiones antes de un alcance distinto; no improvisar localStorage |
| R-06 | Respuestas V2 parcialmente permisivas en OpenAPI | Medio, deriva contractual | Contrastar código/cliente y tipar futuras respuestas con compatibilidad/regresión explícitas |
| R-07 | Pruebas Chromium/axe no cubren lector real, Firefox/Safari o móvil nativo | Medio | Acordar plataformas de evaluación; pruebas dirigidas RC2 si se exigen |
| R-08 | Avisos de dependencias Newman y deprecación Starlette/httpx | Impacto pendiente de auditoría, no vulnerabilidad confirmada | Auditar/actualizar en tarea independiente con locks y regresión; no atribuir CVE no verificados |
| R-09 | Documento histórico dice CPE/CVE/nullable pendientes aunque Backend RC1 ya los implementa | Medio, decisiones basadas en estado antiguo | Citar fecha/commit; conservar originales y usar esta matriz actualizada |
| R-10 | Tag visto como aprobación final o release multirrepo atómica | Medio | Tag de candidato solo tras decisión posterior; manifiesto completo y firmas separadas |

Responsables, fechas objetivo y aceptación de riesgo: **por acordar entre Jesel
y Rudi**. El plan RC2 vincula cada tratamiento; aquí no se impone una asignación
personal ni se altera D-002.
