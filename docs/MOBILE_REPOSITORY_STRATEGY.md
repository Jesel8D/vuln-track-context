# Ubicación de la aplicación móvil

**PENDIENTE:** ubicación y organización. Ninguna opción está aprobada. El repositorio `frontend` no contiene código en la inspección del 30-10-2026; no se creó ningún directorio.

| Opción | Equipo y CI/CD | Tipos/contratos y dependencias | Versionado y costos |
|---|---|---|---|
| Repositorio móvil independiente | Aísla cambios, acceso y pipeline; para dos personas añade repositorio, permisos y revisión. | Clientes se coordinan por contrato API versionado o generación; compartir código requiere paquete o duplicación controlada. | Versiones y releases móviles independientes; más sincronización entre repos. |
| Móvil dentro de `frontend` | Una revisión y coordinación sencillas, con CI por rutas para web/móvil; riesgos de cambios simultáneos. | Dos aplicaciones separadas; compartir tipos solo si contratos se aprueban y se configura workspace. Dependencias nativas no deben mezclarse sin necesidad. | Una historia Git; releases y artefactos pueden seguir separados. Hace que frontend ya no signifique solo web. |
| Monorepositorio de clientes | Cambios atómicos de contrato y clientes, CI y revisiones comunes. | Workspaces facilitan paquetes compartidos, pero duplicados y resolución de dependencias nativas agregan complejidad. | Versionado conjunto o independiente requiere política; puede necesitar reestructurar repositorios y acuerdos. |

**PROPUESTA:** ubicar móvil y web como aplicaciones separadas dentro de `frontend`, con contratos de API como frontera inicial y CI separable por aplicación; no obligar a compartir librerías ni adoptar un monorepositorio complejo antes de necesitarlo. Ventaja: menos administración para dos personas; desventaja: mayor acoplamiento de revisiones e identidad del repositorio. Si la institución exige ciclo y permisos separados, recomendar repositorio móvil independiente. Un monorepositorio formal es viable, pero requiere trabajo adicional de workspaces y compatibilidad. La [guía oficial Expo](https://docs.expo.dev/guides/monorepos/) advierte sobre complejidad y duplicidad de dependencias. Fecha de consulta: 30-10-2026.

**PENDIENTE:** autorización de la ubicación, plataforma y distribución de builds, estrategia de versiones, CI, propiedad de contratos compartidos y migración futura. No se crean repositorios ni archivos de aplicación mediante esta propuesta.
