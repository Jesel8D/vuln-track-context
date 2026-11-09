# Fuentes y evidencias

Hora local del cliente: America/Mexico_City (UTC−06); se registran fechas de solicitud en UTC. Datos NVD cambian. No se guardaron claves ni se descargó la base completa.

| Fuente | Evidencia y uso | Limitación |
|---|---|---|
| [NVD Products API](https://nvd.nist.gov/developers/products) | parámetros y diccionario; FUNDAMENTOS, MATRIZ | fecha de acceso 2026-11-06; lectura indexada |
| [NVD Vulnerabilities API](https://nvd.nist.gov/developers/vulnerabilities) | contrato de referencia; FUNDAMENTOS | página devolvió pantalla sin contenido legible en este entorno; comportamiento puntual corroborado con consultas reales |
| [NVD Start Here](https://nvd.nist.gov/developers/start-here) | referencia oficial de cuotas y prácticas | contenido no legible en este entorno: confirmar límites vigentes antes de implementar |
| [NVD CPE FAQ](https://nvd.nist.gov/general/faq-sections/cpe-faqs) | diferencia nombre, criterio y rangos | no implica cobertura completa de NVD |
| [NVD configuraciones de vulnerabilidad](https://nvd.nist.gov/vuln/Vulnerability-Detail-Pages) | AND/OR, componentes y rangos | información externa cambiante |
| [NIST IR 7695](https://csrc.nist.gov/pubs/ir/7695/final) | especificación CPE 2.3 | sintaxis no verifica presencia |
| Código local backend `2cb124d`, database `b493fd1`, propuesta `54b7644`, contexto `d481061` | esquema, contrato y decisiones | lectura estática; no se ejecutaron migraciones |

## Consultas CPE observadas

- `2026-11-06T06:18:05.054538+00:00` — [Cisco RV340: keywordSearch=cisco rv340, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=cisco+rv340&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=75; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:12.764640+00:00` — [TP-Link Archer C7: keywordSearch=tp-link archer c7, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=tp-link+archer+c7&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=17; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:20.458288+00:00` — [MikroTik RB2011: keywordSearch=mikrotik rb2011, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=mikrotik+rb2011&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=5; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:28.115623+00:00` — [Ubiquiti EdgeRouter X: keywordSearch=ubiquiti edgerouter x, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=ubiquiti+edgerouter+x&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=2; primeras 2 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:36.113185+00:00` — [Cisco Catalyst 2960: keywordSearch=cisco catalyst 2960, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=cisco+catalyst+2960&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=104; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:43.942794+00:00` — [TP-Link TL-SG108E: keywordSearch=tp-link tl-sg108e, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=tp-link+tl-sg108e&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=6; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:51.670090+00:00` — [Netgear GS108E: keywordSearch=netgear gs108e, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=netgear+gs108e&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=6; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:18:59.286774+00:00` — [Ubuntu Linux: keywordSearch=canonical ubuntu linux, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=canonical+ubuntu+linux&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=125; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:19:06.943826+00:00` — [Red Hat Enterprise Linux: keywordSearch=red hat enterprise linux, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=red+hat+enterprise+linux&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=633; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:19:14.578547+00:00` — [MikroTik RouterOS: keywordSearch=mikrotik routeros, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=mikrotik+routeros&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=922; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:19:22.238656+00:00` — [Fortinet FortiOS: keywordSearch=fortinet fortios, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=fortinet+fortios&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=333; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.
- `2026-11-06T06:19:29.925700+00:00` — [Juniper Junos: keywordSearch=juniper junos, resultsPerPage=5, startIndex=0](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=juniper+junos&resultsPerPage=5&startIndex=0): HTTP 200, totalResults=1619; primeras 5 entradas en MATRIZ_PRODUCTOS_NVD.md.

## Consultas CVE observadas

- `2026-11-06T06:20:17.054111+00:00` — [Cisco RV340: cpeName, resultsPerPage=2, startIndex=0](https://services.nvd.nist.gov/rest/json/cves/2.0?cpeName=cpe%3A2.3%3Ao%3Acisco%3Asmall_business_rv340_firmware%3A1.0.0.33%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A&resultsPerPage=2&startIndex=0): HTTP 200, totalResults=1; primera página: CVE-2017-6784. Usada en PRUEBAS_CONCEPTO.md; no se consultaron más páginas.
- `2026-11-06T06:20:25.147201+00:00` — [TP-Link TL-SG108E: cpeName, resultsPerPage=2, startIndex=0](https://services.nvd.nist.gov/rest/json/cves/2.0?cpeName=cpe%3A2.3%3Ao%3Atp-link%3Atl-sg108e_firmware%3A1.1.2%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A&resultsPerPage=2&startIndex=0): HTTP 200, totalResults=5; primera página: CVE-2017-8074, CVE-2017-8075. Usada en PRUEBAS_CONCEPTO.md; no se consultaron más páginas.
- `2026-11-06T06:20:33.205761+00:00` — [Ubuntu Linux: cpeName, resultsPerPage=2, startIndex=0](https://services.nvd.nist.gov/rest/json/cves/2.0?cpeName=cpe%3A2.3%3Ao%3Acanonical%3Aubuntu_linux%3A10.04%3A-%3Alts%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A&resultsPerPage=2&startIndex=0): HTTP 200, totalResults=521; primera página: CVE-2008-5983, CVE-2009-2797. Usada en PRUEBAS_CONCEPTO.md; no se consultaron más páginas.

Consulta preliminar adicional `keywordSearch=cisco ios&resultsPerPage=2` el 2026-11-06T06:17:39Z: HTTP 200, 8197 resultados. Las consultas se espaciaron 7 s dentro de cada serie; no se intentó eludir límites. 403/429 no fueron observados en estas peticiones. No se ensayaron códigos de error ni `virtualMatchString`; su comportamiento específico queda pendiente.

## Rutas y commits locales utilizados

- `/home/mz/Desktop/VulnTrack/backend` rama `feat/backend-v1-assets`, `2cb124d7cc41e22a62a305760826240f9208c257`: `app/schemas/assets.py:AssetCreate`, `app/api/router.py:register/list_assets/get_asset`, `app/services/authorization.py` y migración `alembic/versions/0002_asset_owner.py`. Se usan en FORMULARIO, CONTRATO y MATRIZ_COMPATIBILIDAD.
- `/home/mz/Desktop/VulnTrack/database`, `feat/postgresql-database-v1`, `b493fd17e9290ca94fa085d49230073ed1837e1a`: `sql/001_schema.sql` y sus tablas. `origin/rudi` en `54b7644467a1096c011daf3e5c23d7f9a7773f93`: `sql/004_v2_application_model.sql`. Se usan en MODELO_DATOS y MATRIZ_COMPATIBILIDAD.
- `/home/mz/Desktop/VulnTrack/context-general-ia`, `dev/jesel`, `d481061e5ecb53b9a033086481add37f04d8c549`: `context/decisions.json`, `docs/PROJECT_CONTEXT.md`, `docs/REQUIREMENTS.md`, `docs/SECURITY_BOUNDARIES.md`; D-001/D-002 aprobadas, resto sujeto a revisión. `origin/rudi` se revisó en auditoría antecedente; la propuesta SQL V2 no es una decisión aprobada.
- `/home/mz/Desktop/VulnTrack/auditoria-integracion/`: auditoría ejecutiva, comparación, propuesta objetivo, migraciones, plan y decisiones. Informe antecedente: no es evidencia independiente de pruebas actuales.
- `/home/mz/Desktop/vulntrack-database-v2-audit/docs/VALIDACION_SEGURIDAD_V2.md`, rama `audit/database-v2-security-validation`, commit `2c9723146e910aa51211958556961b4da7019ce3`: correcciones/tests previos y validación incompleta. No se reejecutó ninguna suite en esta investigación.

Las consultas no ensayaron actualización incremental, paginación de segunda página, resultados vacíos, 403/429, ni verificación de hardware real. Las cuotas vigentes de NVD y los parámetros de rangos deben contrastarse con documentación legible antes de implementar; no se presenta una cifra de cuota como verificada aquí.
