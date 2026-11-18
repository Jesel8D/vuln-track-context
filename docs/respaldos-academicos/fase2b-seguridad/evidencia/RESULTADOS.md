> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Evidencia de entrega — Fase 2B

Las pruebas se ejecutaron en PostgreSQL desechable, perfiles nuevos y fixtures
propios. Ninguna prueba usa la base personal como entorno adversarial.

- Resumen de validación y conteos (referencia local no incluida).
- 45 comprobaciones de DNS/TLS/red/recursos (referencia local no incluida).
- Reinicio, backup/restore separado y compatibilidad (referencia local no incluida).
- Captura de escritorio, 1440 px (referencia local no incluida).
- Captura móvil, 390 px (referencia local no incluida).

Las capturas muestran cuentas y destinos sintéticos de prueba después de revocar
el desafío, sin instrucciones ni tokens. La URL de ejemplo no fue contactada.
Los informes Newman completos y logs permanecen privados e ignorados por Git;
solo se entregan aquí conteos y resultados sanitizados.

`network-lab-results.json` identifica la imagen efectivamente ensayada. Su
`production_egress_guarantee=false` y `runtime_verification_enabled=false`
permanecen explícitos. Una IP con apariencia pública es un alias local del
namespace sin rutas externas. No representa contacto con el titular de esa IP.

Los conteos de instalación existente y nueva repiten escenarios; no deben
sumarse como pruebas únicas. Las advertencias heredadas se registran en el
[reporte completo](../../REPORTE_FASE2B_SEGURIDAD.md).
