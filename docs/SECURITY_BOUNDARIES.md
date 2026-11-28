# Límites de seguridad

> **Actualización de estado — 2026-11-27:** producto web únicamente (D-003); el cliente móvil queda descartado. NVD live es una capacidad opt-in del backend, no se activa por una bandera pública del frontend. ZAP no es operativo dentro del alcance MVP aprobado individualmente. Véase [pendientes actuales](REMAINING_WORK_2026-11-27.md).

## Reglas establecidas

- **ESTABLECIDA:** NVD se consulta sin replicar su base de datos completa. Una persona selecciona el CPE entre candidatos; los CVE se presentan como potencialmente aplicables hasta su verificación, sin afirmar coincidencia automática exacta.
- **PENDIENTE:** ZAP Baseline no es un servicio operativo del MVP actual. No ejecutar ZAP ni pruebas activas de dispositivos o redes. Una fase futura deberá diseñar y probar autorización de objetivos, lista permitida, aislamiento y política de salida.
- **ESTABLECIDA:** las observaciones de activos son no sensibles. No almacenar contraseñas administrativas de routers ni secretos de clientes; no incluir credenciales, tokens ni claves en la documentación.

## Medidas y definiciones pendientes

- **ESTABLECIDA:** el código implementa autenticación, autorización por propietario en V1 y por workspace en V2, con sesiones JWT y límites descritos en la documentación de los repositorios.
- **PENDIENTE:** filtro de salida de red en infraestructura, revocación individual de JWT, límites de abuso compartidos entre réplicas, análisis actual de imágenes y validación del host/respaldo.
- **PENDIENTE:** ratificación técnica conjunta del alcance aprobado individualmente; consultar [registro](MVP_APPROVAL_RECORD.md) y [revisión de seguridad](SECURITY_CONTROLS_REVIEW_2026-11-27.md).
