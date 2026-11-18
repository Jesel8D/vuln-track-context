> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Guion académico RC1 — 13 minutos

**PROPUESTA de demostración de V2; no aprobación de alcance.** Se explica desde
el inicio que el MVP histórico administraba dispositivos y la pantalla actual
administra aplicaciones/tecnologías por workspace. No presentar uno como el otro.
Se usan capturas NVD: no consultas en vivo ni objetivos externos.

## Preparación (antes de proyectar)

1. Abrir Ubuntu y comprobar RC1 según [guía existente](../INICIAR_VULNTRACK_RC1.md).
   Web http://127.0.0.1:5177, API http://127.0.0.1:8004/docs. No parar ni actualizar
   la instalación habitual de 5173. Verificar el aviso «NVD de fixture».
2. Elegir modalidad: **recorrido nuevo**, recomendado para observar escrituras,
   o **inspección de dataset existente** si se quiere evitar cambios. Anotarla en
   el checklist. Preparar una contraseña privada; no proyectar terminales con ella.
3. Dataset existente: acceso en
   `/home/USUARIO/VulnTrack-rc1/vuln-track-backend/.local/rc1/demo-access.json`.
   Consultarlo solo localmente. No adjuntar el archivo ni repetir
   `rc1_acceptance --phase create`: eso cambiaría qué cuenta queda como demo.
4. No abrir enlaces a NVD, documentación ZAP o URLs de inventario. Los recursos
   de la UI y la API son locales. Mostrar resultados capturados no verifica un
   producto instalado ni control del dominio example.com.

## Recorrido nuevo (13 minutos orientativos)

| Minuto | Operación y explicación | Resultado / checklist |
|---|---|---|
| 0:00–1:00 | «VulnTrack enlaza inventario, identidad CPE, evidencia CVE y revisión humana. Esta es la candidata web V2; el alcance final con dispositivos y móvil sigue pendiente.» Mostrar aviso fixture | CF-01–04; no afirmar detección automática |
| 1:00–2:15 | Crear cuenta normal con usuario único y contraseña privada; login | Identidad, workspace personal y owner; no administrador global. CF-05/06 |
| 2:15–3:30 | Dashboard vacío → Registrar aplicación: `DEMO aceptación RC1 - Portal ficticio`, descripción FICTICIO. Añadir URL `https://example.com/rc1-demo` como referencia de inventario | Una aplicación guardada, sin conexión al destino. CF-07 |
| 3:30–4:30 | Añadir tecnología manual: vendor `cisco`, product `small_business_rv340_firmware`, version `1.0.0.33` | Explicar que es un ejemplo de catálogo, no firmware descubierto en la URL. CF-08 |
| 4:30–6:00 | Buscar `RV340`, leer candidato, seleccionar radio y pulsar Confirmar CPE | Confirmación explícita. CF-09/10 |
| 6:00–7:15 | Pulsar Consultar CVE, observar estado/progreso; si termina rápido, mostrar estado completado persistente | Worker/API reales con respuesta externa reemplazada por captura. CF-11 |
| 7:15–8:45 | Abrir CVE-2017-6784 y explicar AND/condiciones de hardware. Revisar manteniendo Indeterminado, con motivo y evidencia documentales de demo | `undetermined` y `reviewed`, nunca `confirmed`. CF-12/13 |
| 8:45–9:45 | Agregar recomendación manual: «DEMO: revisar el aviso capturado y las condiciones AND; no concluir vulnerabilidad en un sistema real.» Estado pendiente | Medida propuesta, no reparación efectuada. CF-14 |
| 9:45–10:45 | Historial y dashboard; explicar conteo de una consulta y posible cero abiertos tras revisión | La revisión no elimina el CVE ni declara al sistema seguro. CF-15/16 |
| 10:45–11:45 | Análisis de seguridad: preparación, declaración distinta de control técnico y Baseline disabled. Para estados revocado/expirado, usar después el dataset preparado o su evidencia | No emitir/verificar desafíos como parte necesaria del guion; no ZAP. CF-18/19 |
| 11:45–12:30 | Cerrar sesión, entrar con la misma cuenta y volver al historial | Datos conservados; recargar requiere login. CF-17 |
| 12:30–13:00 | Señalar decisiones de alcance que ambos deben resolver y el resultado de revisión individual | No presentar la demo completada como aceptación definitiva |

El CPE esperado es:

```text
cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*
```

Su ID capturado es `C817E5BA-62C1-41E7-A15C-FB851FEA761E`. La captura se conserva
en Backend `tests/fixtures/nvd_cpe_verified.json` y `nvd_cve_verified.json`; la
fábrica activa usa `original_and=True`. No cambiarla a OR para producir un
resultado más llamativo. El ejemplo no prueba vulnerabilidad de la URL.

## Modalidad de inspección sin escrituras (10 minutos)

Iniciar sesión con el acceso privado existente, mostrar aplicación, tecnología,
CPE ya confirmado, análisis y revisión guardados, recomendación, historial,
dashboard y seguridad con desafío expirado. Abrir el formulario de alta para
explicar registro sin enviarlo. **Declarar qué acciones se están inspeccionando
como evidencia previa**. No volver a confirmar el mismo CPE ni revisar de nuevo
un hallazgo `reviewed`; el contrato evita sobrescribir esa revisión.

Las capturas de esta revisión (referencia local no incluida) y la evidencia
RC1 anterior respaldan esta modalidad, pero no sustituyen un alta/revisión nueva
si ambos la establecen como criterio obligatorio. En 15 minutos se puede añadir
la segunda cuenta ficticia para mostrar que su workspace no contiene la aplicación
principal. No hacer demostraciones de rol mediante manipulación del navegador.

## Si algo falla

Respetar Retry-After de 429; no quitar cuotas. Ante 401 tras recarga, hacer login.
Ante respuesta perdida, revisar inventario/historial antes de crear otra intención.
Si faltan datos, usar el registro de error con pasos y evidencia; no restaurar
sobre development ni fabricar un resultado. Un restore en vivo no cabe en este
guion: revisar la evidencia de las 13 comprobaciones anteriores o programar un
ensayo separado en volumen nuevo. Nunca habilitar NVD live, verificación remota,
egreso o ZAP para completar la presentación.
