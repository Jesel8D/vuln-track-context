# Requisitos

## Funcionales

- **ESTABLECIDA:** registrar activos desde la aplicación móvil con tipo de dispositivo, fabricante, modelo, versión de firmware o software e identificador o etiqueta del sitio; la etiqueta de inventario y las observaciones no sensibles son opcionales.
- **ESTABLECIDA:** enviar el registro al backend y conservar los datos introducidos si el envío falla para permitir otro intento.
- **ESTABLECIDA:** consultar y visualizar activos en la web y ofrecer administración general de activos.
- **PENDIENTE:** definir el alcance y las reglas de actualización detallada de activos.
- **PENDIENTE:** definir el alcance y las reglas de eliminación de activos.
- **ESTABLECIDA:** buscar candidatos CPE, presentar opciones y permitir que una persona seleccione el correspondiente; consultar CVE en NVD REST API 2.0 para el CPE seleccionado y mostrar resultados como potencialmente aplicables hasta su verificación.
- **ESTABLECIDA:** mostrar resultados y hallazgos, un dashboard general y permitir solicitar y consultar análisis web controlados con ZAP Baseline.

## No funcionales

- **ESTABLECIDA:** enfoque online-first; conservar datos tras un fallo de envío no implica sincronización offline completa.
- **ESTABLECIDA:** el flujo móvil → API → PostgreSQL → web tiene prioridad.
- **PENDIENTE:** definir objetivos medibles de rendimiento, disponibilidad y accesibilidad; no se dispone de valores aprobados.

## Restricciones

- **ESTABLECIDA:** no replicar completamente NVD ni afirmar coincidencia automática exacta entre activo y CPE.
- **ESTABLECIDA:** análisis aislados y controlados únicamente para objetivos web autorizados e incluidos previamente en una lista permitida; no permitir URLs arbitrarias, Active Scan, ni escanear routers, firewalls, redes completas o dispositivos físicos.
- **ESTABLECIDA:** no presentar Baseline como garantía de detección de SQL Injection u otras vulnerabilidades específicas.
- **PENDIENTE:** aprobar las medidas técnicas de aislamiento, autenticación y autorización; consulta [seguridad](SECURITY_BOUNDARIES.md).

## Exclusiones del MVP

**ESTABLECIDA:** CISA KEV, Redis, exportación PDF, lectura QR o cámara, GPS, sincronización offline completa, Active Scan, almacenamiento de contraseñas administrativas de routers y almacenamiento de secretos de clientes no forman parte del MVP. Su incorporación requiere aprobación explícita.
