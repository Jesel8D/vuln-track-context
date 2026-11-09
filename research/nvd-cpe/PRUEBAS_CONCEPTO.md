# Tres pruebas de concepto documentales

Entradas de formulario y confirmaciones son **SIMULADAS**, no se inspeccionó equipo ni se contactó un dispositivo. Las búsquedas CPE y CVE son **VERIFICADAS EN NVD**; identifican entradas y resultados, no confirman vulnerabilidad del activo. Los valores CVE se limitaron a la primera página; fecha/hora UTC y URL registradas a continuación.

## A — Router
1. **Usuario simulado:** router Cisco RV340; firmware 1.0.0.33; sitio Laboratorio A.
2. **Inventario conceptual:** asset propio con tipo, fabricante, modelo, versión, sitio; UUID y propiedad serían generados por backend. No existe endpoint de análisis hoy.
3. **CPE:** [`keywordSearch=cisco rv340`](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=cisco+rv340&resultsPerPage=5&startIndex=0) a `2026-11-06T06:18:05.054538+00:00`: total 75; candidatos de primera página:
   - `cpe:2.3:h:cisco:small_business_rv340_router:-:*:*:*:*:*:*:*` — `414CFE6C-9B18-472A-B6DA-F47000754714`; deprecated=false.
   - `cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*` — `C817E5BA-62C1-41E7-A15C-FB851FEA761E`; deprecated=false.
4. **Selección simulada para ilustración:** `cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*`; ID `C817E5BA-62C1-41E7-A15C-FB851FEA761E`. Una persona debería confirmar que corresponde al producto y versión observados.
5. **Consulta real:** [`cpeName` y primera página de 2](https://services.nvd.nist.gov/rest/json/cves/2.0?cpeName=cpe%3A2.3%3Ao%3Acisco%3Asmall_business_rv340_firmware%3A1.0.0.33%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A&resultsPerPage=2&startIndex=0) a `2026-11-06T06:20:17.054111+00:00`; HTTP 200; totalResults=1; primera página: CVE-2017-6784.
6. **Incertidumbre:** El catálogo también muestra el hardware RV340: no asumir que el firmware basta para satisfacer toda configuración. El total no es número de vulnerabilidades confirmadas ni demuestra que todas las páginas se hayan leído.
7. **Persistencia propuesta:** selección con fuente y fecha; `analysis_runs` con CPE snapshot, actor, fecha y cobertura; `findings` por CVE de páginas realmente recuperadas, CVSS/evidencia si está disponible y estado potencial o indeterminado según modelo aprobado. No hay escritura real.
8. **Dashboard propuesto:** «Resultados potenciales; revisión necesaria», estado de cobertura parcial hasta obtener todas las páginas y aviso de datos no confirmados.

## B — Switch
1. **Usuario simulado:** switch TP-Link TL-SG108E; firmware 1.1.2; sitio Laboratorio B.
2. **Inventario conceptual:** asset propio con tipo, fabricante, modelo, versión, sitio; UUID y propiedad serían generados por backend. No existe endpoint de análisis hoy.
3. **CPE:** [`keywordSearch=tp-link tl-sg108e`](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=tp-link+tl-sg108e&resultsPerPage=5&startIndex=0) a `2026-11-06T06:18:43.942794+00:00`: total 6; candidatos de primera página:
   - `cpe:2.3:h:tp-link:tl-sg108e:-:*:*:*:*:*:*:*` — `08D77187-DBD3-4F0A-91CB-19A56A672A2A`; deprecated=false.
   - `cpe:2.3:o:tp-link:tl-sg108e_firmware:1.1.2:*:*:*:*:*:*:*` — `9F68A907-08BA-4B63-BFBE-0B3AD501B701`; deprecated=false.
4. **Selección simulada para ilustración:** `cpe:2.3:o:tp-link:tl-sg108e_firmware:1.1.2:*:*:*:*:*:*:*`; ID `9F68A907-08BA-4B63-BFBE-0B3AD501B701`. Una persona debería confirmar que corresponde al producto y versión observados.
5. **Consulta real:** [`cpeName` y primera página de 2](https://services.nvd.nist.gov/rest/json/cves/2.0?cpeName=cpe%3A2.3%3Ao%3Atp-link%3Atl-sg108e_firmware%3A1.1.2%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A&resultsPerPage=2&startIndex=0) a `2026-11-06T06:20:25.147201+00:00`; HTTP 200; totalResults=5; primera página: CVE-2017-8074, CVE-2017-8075.
6. **Incertidumbre:** CVE-2017-8074 muestra configuración AND con firmware vulnerable y hardware TL-SG108E como condición de plataforma (`vulnerable=false`); hay que verificar ambos. El total no es número de vulnerabilidades confirmadas ni demuestra que todas las páginas se hayan leído.
7. **Persistencia propuesta:** selección con fuente y fecha; `analysis_runs` con CPE snapshot, actor, fecha y cobertura; `findings` por CVE de páginas realmente recuperadas, CVSS/evidencia si está disponible y estado potencial o indeterminado según modelo aprobado. No hay escritura real.
8. **Dashboard propuesto:** «Resultados potenciales; revisión necesaria», estado de cobertura parcial hasta obtener todas las páginas y aviso de datos no confirmados.

## C — Servidor
1. **Usuario simulado:** servidor ficticio; Canonical Ubuntu Linux 10.04 LTS; sitio Laboratorio C.
2. **Inventario conceptual:** asset propio con tipo, fabricante, modelo, versión, sitio; UUID y propiedad serían generados por backend. No existe endpoint de análisis hoy.
3. **CPE:** [`keywordSearch=canonical ubuntu linux`](https://services.nvd.nist.gov/rest/json/cpes/2.0?keywordSearch=canonical+ubuntu+linux&resultsPerPage=5&startIndex=0) a `2026-11-06T06:18:59.286774+00:00`: total 125; candidatos de primera página:
   - `cpe:2.3:o:canonical:ubuntu_linux:10.04:-:lts:*:*:*:*:*` — `A5A75772-10D6-4D6F-BC97-084D70FDDE7E`; deprecated=false.
   - `cpe:2.3:o:canonical:ubuntu_linux:9.10:*:*:*:*:*:*:*` — `3734F34C-BA2F-4E82-8393-09E571A138C6`; deprecated=false.
4. **Selección simulada para ilustración:** `cpe:2.3:o:canonical:ubuntu_linux:10.04:-:lts:*:*:*:*:*`; ID `A5A75772-10D6-4D6F-BC97-084D70FDDE7E`. Una persona debería confirmar que corresponde al producto y versión observados.
5. **Consulta real:** [`cpeName` y primera página de 2](https://services.nvd.nist.gov/rest/json/cves/2.0?cpeName=cpe%3A2.3%3Ao%3Acanonical%3Aubuntu_linux%3A10.04%3A-%3Alts%3A%2A%3A%2A%3A%2A%3A%2A%3A%2A&resultsPerPage=2&startIndex=0) a `2026-11-06T06:20:33.205761+00:00`; HTTP 200; totalResults=521; primera página: CVE-2008-5983, CVE-2009-2797.
6. **Incertidumbre:** La búsqueda recoge una versión antigua; usar solo como dato de demostración histórica, no como recomendación de despliegue. El total no es número de vulnerabilidades confirmadas ni demuestra que todas las páginas se hayan leído.
7. **Persistencia propuesta:** selección con fuente y fecha; `analysis_runs` con CPE snapshot, actor, fecha y cobertura; `findings` por CVE de páginas realmente recuperadas, CVSS/evidencia si está disponible y estado potencial o indeterminado según modelo aprobado. No hay escritura real.
8. **Dashboard propuesto:** «Resultados potenciales; revisión necesaria», estado de cobertura parcial hasta obtener todas las páginas y aviso de datos no confirmados.


## Fragmentos de evidencia de las respuestas CVE

Los campos siguientes proceden de las respuestas HTTP observadas; las descripciones y métricas no son evaluaciones del activo. Se conserva aquí un fragmento acotado de configuración para reproducibilidad.

### Cisco RV340

```json
{
  "id": "CVE-2017-6784",
  "sourceIdentifier": "psirt@cisco.com",
  "published": "2017-08-17T20:29:00.823",
  "lastModified": "2026-06-17T01:23:05.697",
  "vulnStatus": "Modified",
  "configurations": [
    {
      "operator": "AND",
      "nodes": [
        {
          "operator": "OR",
          "negate": false,
          "cpeMatch": [
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.30:*:*:*:*:*:*:*",
              "matchCriteriaId": "A6F2025A-9AF4-4DFD-80BF-844EFC6C103A"
            },
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.0.33:*:*:*:*:*:*:*",
              "matchCriteriaId": "8750A439-1D5F-4D40-9875-D5CD0C116C11"
            },
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.1.9:*:*:*:*:*:*:*",
              "matchCriteriaId": "E1D63FAC-EE0A-4CD4-B733-A96170E32F2B"
            },
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:o:cisco:small_business_rv340_firmware:1.0.1.16:*:*:*:*:*:*:*",
              "matchCriteriaId": "DDBF80BD-1C86-4FFE-9EF5-ADF8B3D179B7"
            }
          ]
        },
        {
          "operator": "OR",
          "negate": false,
          "cpeMatch": [
            {
              "vulnerable": false,
              "criteria": "cpe:2.3:h:cisco:small_business_rv340:-:*:*:*:*:*:*:*",
              "matchCriteriaId": "A85968D9-1F54-48DC-9AB0-04563B98A627"
            }
          ]
        }
      ]
    }
  ]
}
```

### TP-Link TL-SG108E

```json
{
  "id": "CVE-2017-8074",
  "sourceIdentifier": "cve@mitre.org",
  "published": "2017-04-23T16:59:00.140",
  "lastModified": "2026-06-17T01:25:44.600",
  "vulnStatus": "Modified",
  "configurations": [
    {
      "operator": "AND",
      "nodes": [
        {
          "operator": "OR",
          "negate": false,
          "cpeMatch": [
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:o:tp-link:tl-sg108e_firmware:1.1.2:*:*:*:*:*:*:*",
              "matchCriteriaId": "072964A1-9F2B-4FE8-954E-6059728663EE"
            }
          ]
        },
        {
          "operator": "OR",
          "negate": false,
          "cpeMatch": [
            {
              "vulnerable": false,
              "criteria": "cpe:2.3:h:tp-link:tl-sg108e:-:*:*:*:*:*:*:*",
              "matchCriteriaId": "537907B5-7EAE-453A-81C6-8CF10498E7EB"
            }
          ]
        }
      ]
    }
  ]
}
```

### Ubuntu Linux

```json
{
  "id": "CVE-2008-5983",
  "sourceIdentifier": "cve@mitre.org",
  "published": "2009-01-28T02:30:00.233",
  "lastModified": "2026-06-16T23:01:21.217",
  "vulnStatus": "Modified",
  "configurations": [
    {
      "nodes": [
        {
          "operator": "OR",
          "negate": false,
          "cpeMatch": [
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:a:python:python:*:*:*:*:*:*:*:*",
              "versionEndExcluding": "2.6.6",
              "matchCriteriaId": "772EB861-7A7A-4593-9D0D-B343AD1BEEE3"
            },
            {
              "vulnerable": true,
              "criteria": "cpe:2.3:a:python:python:*:*:*:*:*:*:*:*",
              "versionStartIncluding": "3.1.0",
              "versionEndExcluding": "3.1.3",
              "matchCriteriaId": "6B1FC450-BBF4-45E2-873E-3EB2CCA23845"
            }
          ]
        }
      ]
    }
  ]
}
```

**Cobertura observada:** RV340 devolvió 1 de 1 CVE y por tanto completó esa consulta puntual; TL-SG108E devolvió 2 de 5 y Ubuntu 2 de 521, ambas parciales. Completa significa que se recuperaron todos los resultados de esos parámetros en esa respuesta, no que el catálogo o la correlación sean exhaustivos.
