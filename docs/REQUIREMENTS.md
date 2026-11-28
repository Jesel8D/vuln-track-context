# Requisitos

> **Actualización de estado — 2026-11-27:** D-003 limita el producto a la aplicación web; no se construirá cliente móvil. El alcance MVP del 2026-11-08 fue aprobado individualmente por Jesel; la ratificación de Rudi y los detalles técnicos globales siguen **PENDIENTES**. La operación ZAP queda fuera del MVP aprobado individualmente. Véanse [decisiones](DECISIONS_AND_OPEN_QUESTIONS.md), [registro de aprobación](MVP_APPROVAL_RECORD.md) y [pendientes actuales](REMAINING_WORK_2026-11-27.md). Los requisitos móviles de documentos anteriores son antecedentes.

## Funcionales

- **ESTABLECIDA:** registrar activos desde la aplicación web con tipo de dispositivo, fabricante, modelo, versión de firmware o software e identificador o etiqueta del sitio; la etiqueta de inventario y las observaciones no sensibles son opcionales.
- **ESTABLECIDA:** enviar el registro al backend desde la web y conservar los datos introducidos si el envío falla para permitir otro intento.
- **ESTABLECIDA:** consultar y visualizar activos en la web y ofrecer administración general de activos.
- **PENDIENTE:** definir el alcance y las reglas de actualización detallada de activos.
- **PENDIENTE:** definir el alcance y las reglas de eliminación de activos.
- **ESTABLECIDA:** buscar candidatos CPE, presentar opciones y permitir que una persona seleccione el correspondiente; consultar CVE en NVD REST API 2.0 para el CPE seleccionado y mostrar resultados como potencialmente aplicables hasta su verificación.
- **ESTABLECIDA:** mostrar resultados y hallazgos y un dashboard general. ZAP Baseline no es una capacidad operativa del MVP aprobado; una fase futura requiere decisión y controles de seguridad separados.

## No funcionales

- **ESTABLECIDA:** enfoque online-first; conservar datos tras un fallo de envío no implica sincronización offline completa.
- **ESTABLECIDA:** el flujo de producto es navegador web → API → PostgreSQL (D-003).
- **PENDIENTE:** definir objetivos medibles de rendimiento, disponibilidad y accesibilidad; no se dispone de valores aprobados.

## Restricciones

- **ESTABLECIDA:** no replicar completamente NVD ni afirmar coincidencia automática exacta entre activo y CPE.
- **ESTABLECIDA:** análisis aislados y controlados únicamente para objetivos web autorizados e incluidos previamente en una lista permitida; no permitir URLs arbitrarias, Active Scan, ni escanear routers, firewalls, redes completas o dispositivos físicos.
- **ESTABLECIDA:** no presentar Baseline como garantía de detección de SQL Injection u otras vulnerabilidades específicas.
- **PENDIENTE:** aprobar las medidas técnicas de aislamiento, autenticación y autorización; consulta [seguridad](SECURITY_BOUNDARIES.md).

## Exclusiones del MVP

**ESTABLECIDA:** aplicación móvil, CISA KEV, Redis, exportación PDF, lectura QR o cámara, GPS, sincronización offline completa, ZAP/Active Scan, almacenamiento de contraseñas administrativas de routers y almacenamiento de secretos de clientes no forman parte del MVP. Su incorporación requiere aprobación explícita.
