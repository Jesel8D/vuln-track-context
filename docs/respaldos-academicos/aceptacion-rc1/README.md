> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# VulnTrack V2 RC1 — paquete de aceptación académica

**Preparada para que Jesel y Rudi realicen la revisión formal del candidato V2.**
La aceptación, el alcance definitivo y las firmas siguen **PENDIENTES**. Esta
conclusión técnica no convierte RC1 en producción ni en versión académica final.

Fecha local: 2026-11-15; las marcas UTC de evidencia corresponden al 16 de noviembre.
La revisión conserva los commits RC1, los cuatro repositorios originales y sus
bases. Solo añade este paquete documental local; no crea commits, pushes o tags.

| Documento | Para qué sirve |
|---|---|
| [1. Matriz de requisitos y cobertura](01_MATRIZ_REQUISITOS_COBERTURA.md) | RF/RNF originales, 38 AC, diferencias V1/V2 y exclusiones |
| [2. Checklist de aceptación](02_CHECKLIST_ACEPTACION.md) | Pasos verificables y casillas reservadas a los revisores |
| [3. Guion de demostración](03_GUION_DEMOSTRACION.md) | Recorrido manual de 13 minutos con alternativa sin modificar el dataset |
| [4. Decisiones pendientes](04_DECISIONES_PENDIENTES.md) | Opciones, impactos, recomendaciones y registro individual Jesel/Rudi |
| [5. Defectos y riesgos](05_DEFECTOS_RIESGOS.md) | Defectos corregidos, brechas de alcance y riesgos con severidad |
| [6. Manifiesto RC1](06_MANIFIESTO_RC1.md) | Hashes completos, procedencia de pruebas, restricciones y propuesta de tag |
| [7. Propuesta RC2](07_PROPUESTA_RC2.md) | Trabajo priorizado y condicionado a las decisiones, sin implementación |

El manifiesto legible por máquina (referencia local no incluida) vincula fuentes y hashes
de este paquete. Evidencia nueva: 11 comprobaciones de entorno/código (referencia local no incluida),
9 comprobaciones de navegador/API (referencia local no incluida) y
referencias remotas/tag ausente (referencia local no incluida). No son una nueva
ejecución de las suites completas. Estas se conservan en el
[informe RC1 anterior](../REPORTE_V2_RC1.md) y sus resultados originales.

RC1: http://127.0.0.1:5177 · API: http://127.0.0.1:8004/docs.
[Inicio, parada y backup](../INICIAR_VULNTRACK_RC1.md).
El acceso ficticio permanece privado en Backend `.local/rc1/demo-access.json`;
no se incluye en el paquete. Las capturas nuevas muestran únicamente el dataset
ficticio autenticado, sin contraseñas: dashboard (referencia local no incluida)
y historial (referencia local no incluida).

Para declarar una versión académica definitiva deben acordar, como mínimo, el
dominio de dispositivos/aplicaciones, la integración de V1, workspaces, móvil,
ZAP, los criterios obligatorios y la aprobación de ambos. Si se mantiene el MVP
original con dispositivos y móvil, la UI V2 actual no basta para darlo por cumplido.
