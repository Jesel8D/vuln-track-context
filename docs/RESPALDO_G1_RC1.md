# Inventario G1 del paquete de aceptación V2 RC1

**PROPUESTA**, 18/11/2026. Este inventario es documental y no modifica la aprobación del MVP, no integra V2, no habilita ZAP ni reemplaza la ratificación de Rudi. Fuentes: paquete local de aceptación, auditoría Git y ramas remotas RC1. Ninguna copia de los originales se publica o se altera durante G1.

Los 16 archivos del paquete fueron identificados y sus bytes tienen los SHA-256 siguientes. La lista no es autorización para subirlos: los originales viven fuera de Git. Las dos capturas fueron revisadas visualmente y muestran datos y usuario de demostración, pero incluyen identificadores; conservar en respaldo privado y revisar si se desean compartir. Algunos textos/JSON/scripts contienen rutas personales o referencias a entornos; sanear y revisar antes de cualquier publicación. Las búsquedas de patrones no garantizan ausencia de datos sensibles.

Comprobación G1: los 15 archivos enumerados en el `SHA256SUMS.txt` original coinciden con sus sumas. El propio archivo de sumas se incluye abajo con hash independiente; no se modificaron originales.

| Archivo local bajo `aceptacion-rc1/` | SHA-256 | Propuesta de manejo |
|---|---|---|
| `01_MATRIZ_REQUISITOS_COBERTURA.md` | `541a64229a772cf119e033a4e52a13fc8402d8b997b53f26fab78ef96d5c794b` | Documentación académica: candidata tras revisión humana y comprobación de enlaces. |
| `02_CHECKLIST_ACEPTACION.md` | `2d4d6cc2bcb715b214941b7cad3e3e2a9cfda5a9e5c342426e320fc07ff8abaf` | Documentación académica: candidata tras revisión humana y comprobación de enlaces. |
| `03_GUION_DEMOSTRACION.md` | `804c3b51d555492a6aa1e8a5a94add7c216d366e5c45924497bb876cf7c1e939` | Documentación: contiene ruta personal; versión saneada y revisión pendientes. |
| `04_DECISIONES_PENDIENTES.md` | `bde2353dadde50e8baa9ac1049724163a80d8934f3134875a0396cc389664571` | Documentación académica: candidata tras revisión humana y comprobación de enlaces. |
| `05_DEFECTOS_RIESGOS.md` | `57469af17100b00fe3ca161082358fc22a96a1172c8665d293c2afd1f554a255` | Documentación académica: candidata tras revisión humana y comprobación de enlaces. |
| `06_MANIFIESTO_RC1.md` | `61bf92ac3183bdb4e63816a8c9787489f9c6f66e7b8591e3922d8563d326847c` | Documentación: contiene ruta personal; versión saneada y revisión pendientes. |
| `07_PROPUESTA_RC2.md` | `a6c53fa89c06c9090e2f99cdd1a349f2c43214a3d80432b4b9e1881bad443cae` | Documentación académica: candidata tras revisión humana y comprobación de enlaces. |
| `evidencia/dashboard-actual.png` | `a9bc0850c254f6352f38674f6c54bede5299e85995b0dea8114beecf5c8397fe` | Evidencia: mantener privada; revisar identificadores antes de compartir. |
| `evidencia/historial-actual.png` | `670bbe758bbababeb1628e57ca6b036d1ac721a05ca808a6d8001ece2a272bc3` | Evidencia: mantener privada; revisar identificadores antes de compartir. |
| `evidencia/inspeccionar_navegador.mjs` | `8e5652e15dd25ed0d903e2e978e834bc2fe7b44fc72b2095316307f41f038b97` | Script de inspección: contiene ruta local; conservar privado y revisar antes de reutilizar. |
| `evidencia/navegador-actual.json` | `92840d66338cb70498522e1e7b0cf7e303445f273b74a1aaae8d3b49a0459fd0` | Manifiesto/evidencia: preservar privado; revisar rutas y datos antes de publicar una versión saneada. |
| `evidencia/referencias-git.json` | `e30bf9a3b106152b2a1644233df17972ce0a63db51efd9e9d2fe8bdb156bb4ae` | Manifiesto/evidencia: preservar privado; revisar rutas y datos antes de publicar una versión saneada. |
| `evidencia/verificacion-actual.json` | `fa3fe578caeb26ee33e2f223cbf3f307ce3ecbd3fc32e7f8b7da14f97af9d6ae` | Manifiesto/evidencia: preservar privado; revisar rutas y datos antes de publicar una versión saneada. |
| `manifiesto_rc1.json` | `7bede61e0e6835c7109b4db3b0628810bbb6d5f730fb3375e106b6e8a5a45650` | Manifiesto/evidencia: preservar privado; revisar rutas y datos antes de publicar una versión saneada. |
| `README.md` | `e8950f55d93d50be128e08c377ede8c96306316f83341214ab8167cbc06bd4c5` | Documentación académica: candidata tras revisión humana y comprobación de enlaces. |
| `SHA256SUMS.txt` | `9389621f115bdd03711302038154a568138ee0c6603cfaab85f0355d94a5a604` | Manifiesto original: conservar con el paquete privado; no recalcular sobre archivos alterados. |


## Ramas y decisiones de integración

- Backend `release/v2-rc1-integration` y frontend `release/v2-rc1-web` ya están respaldados en GitHub; database `feat/v2-security-verification-database` y context `docs/mvp-context-consolidation` completan el conjunto RC1 documentado.
- Los checkouts habituales apuntan a ramas anteriores; no se deben hacer merge, rebase, checkout ni cherry-pick automáticamente. Comparar contratos y cambios de esquema, y obtener decisión humana antes de cualquier integración.
- Los documentos `fase1-seguridad/` y `fase2a-seguridad/` coinciden por contenido con puntas remotas; algunas evidencias de 2B/2C y RC1 y todo el paquete de aceptación no se hallaron en la historia remota examinada. Guardar copias cifradas y con acceso limitado fuera de Git cuando exista destino autorizado.
- La selección de qué documentos y capturas pueden entrar en un repositorio público queda **PENDIENTE** de revisión de privacidad, procedencia y autorización. Los nombres de firmas no constituyen aprobación conjunta.
