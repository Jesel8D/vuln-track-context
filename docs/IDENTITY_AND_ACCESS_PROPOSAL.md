# Propuesta de identidad y permisos

**PROPUESTA:** la identidad de VulnTrack, roles, sesiones, permisos y controles concretos requieren aprobación. No hay requisito institucional conocido ni implementación. **ESTABLECIDA:** no almacenar contraseñas administrativas de dispositivos ni secretos de clientes. Un usuario de VulnTrack es distinto de un dispositivo; contraseñas propias del sistema y secretos operativos deben protegerse y no registrarse en código, capturas o logs.

## Alternativas de autenticación

| Opción | Ventajas | Desventajas |
|---|---|---|
| Identidad local y sesiones revocables | Menos dependencias externas; control explícito de bajas y demostración. | Equipo debe gestionar altas, hashes, revocación y protección de credenciales. |
| Proveedor de identidad externo | Reduce parte de la gestión local y puede centralizar identidad. | Requiere proveedor, disponibilidad, configuración y política institucional no conocida; añade integración. |
| Tokens autocontenidos | Pueden verificarse sin consultar sesión en cada petición. | Revocación y almacenamiento seguro requieren diseño adicional; no asumir que cerrar sesión revoca un token ya emitido. |

**PROPUESTA principal:** identidad local si no hay proveedor institucional y sesiones opacas revocables del lado servidor; alternativa proveedor externo si ya existe y está aprobado. Credenciales de VulnTrack con hash adaptativo y sal individual, p. ej. Argon2id con parámetros calibrados; no guardar contraseña en texto. Alta, recuperación y distribución de credenciales de usuarios están **PENDIENTE**. Mensaje de acceso genérico, limitación por cuenta y origen, TLS y auditoría sin secretos; una simple cuota por IP no basta.

**Sesiones propuestas:** expiración configurable, por ejemplo 15 minutos de inactividad y máximo 8 horas, valores sujetos a aprobación; logout y desactivación revocan servidor, con expiración verificada en cada solicitud. Guardar en servidor un derivado no reversible de la credencial de sesión; generar valores de alta entropía. Evitar mostrar valor de sesión. Web: cookie `HttpOnly`, `Secure`, `SameSite` apropiado y protección CSRF con verificación explícita de token/origen para operaciones con efectos; CORS no sustituye CSRF. Móvil: almacenar credencial de sesión solo en almacenamiento seguro de plataforma, no en texto plano o registros; borrar al salir. Definir si el móvil comparte la misma sesión o requiere credencial de corta vida y renovación segura; no prometer revocación de un token sin verificar su estado servidor.

El [borrador anterior de API](API_CONTRACT_DRAFT.md) ilustra `Bearer` para todos los clientes; esta propuesta diferencia cookie web y credencial móvil. Son alternativas aún no aprobadas: elegir una transportación por cliente y actualizar el contrato antes de implementar, sin tratar ambas como acuerdos existentes.

## Roles mínimos de ejemplo

| Operación | Operador de campo | Analista | Administrador |
|---|---|---|---|
| Crear activo | Sí | Sí | Sí |
| Consultar activos autorizados | Sí | Sí | Sí |
| Buscar CPE, seleccionar y consultar CVE | No | Sí | Sí |
| Consultar hallazgos y dashboard autorizados | No | Sí | Sí |
| Solicitar y consultar ZAP sobre destino autorizado | No | Sí, sujeto a permiso de destino | Sí, sujeto a permiso de destino |
| Gestionar usuarios y autorización de destinos | No | No | Sí, con revisión humana según política aprobada |
| Actualizar/eliminar activos | PENDIENTE | PENDIENTE | PENDIENTE |

**PROPUESTA:** roles no sustituyen controles por recurso y destino; negar por defecto, comprobar permisos en servidor y no revelar existencia fuera del alcance. Alcance de recursos, separación entre quien autoriza y quien ejecuta, delegación, duración de acceso y transición de roles están **PENDIENTE**. Que un administrador tenga acceso no implica poder autorizar su propio destino sin revisión, pendiente de decisión. No se inventa una estructura multitenant.

## Verificación antes de habilitar

**PROPUESTA:** probar identidad inválida, sesión caducada/revocada, cookies/CSRF, acceso entre usuarios y recursos, rate limiting, cierre de sesión móvil/web y ausencia de secretos en trazas. Documentar operación de usuarios, restauración y revocación antes de desplegar. Revisión de credenciales operativas y redacción de evidencia ZAP se detallan en [seguridad de integraciones](INTEGRATION_SECURITY_REVIEW.md). Fuente de referencia: [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html), consultada 30-10-2026.
