> Copia hist?rica depurada para respaldo: rutas personales y direcciones privadas se han anonimizado; referencias locales no incluidas se se?alan en el texto. El original permanece en el equipo.

# Propuesta priorizada de RC2

**PROPUESTA, sin ejecución ni aprobación.** RC2 debe cerrar las brechas del
alcance que Jesel/Rudi elijan; no ampliar automáticamente funciones porque estén
en una lista. Mantener main/rudi, V1, RC1 y la base personal protegidos. No se
reabren D-001/D-002 ni se activa ZAP/verificación/egreso por este plan.

## Puerta P0: antes de definir cambios de código

| ID / prioridad | Resultado requerido | Dependencia | Criterio de salida propuesto |
|---|---|---|---|
| RC2-01 / P0 | Cierre explícito de dominio, V1, workspaces y móvil | AA-D01–D04 | Opciones y alcance firmados por ambos; mapa de requisitos que quedan dentro/fuera/pendientes |
| RC2-02 / P0 | Primera entrega y ZAP; funciones obligatorias y metodología fixture | AA-D05–D07 | Lista de criterios obligatorios con IDs; aplazamiento ZAP y límites NVD documentados |
| RC2-03 / P0 | Acta de evaluación RC1 y plan de defectos/excepciones | AA-D08, CF-01–28 | Dos resultados individuales y decisión conjunta; ningún S1/S2 del alcance final sin tratamiento acordado |

Si no hay decisión sobre una función, no implementar el trabajo dependiente por
suposición. Los acuerdos pueden mantener RC1 como piloto V2 y separar otra
entrega del MVP original. La mera publicación/tag no cierra estas puertas.

## Trabajo condicionado al alcance elegido

| ID / prioridad propuesta | Trabajo futuro | Cuándo es obligatorio / impacto | Verificación de salida |
|---|---|---|---|
| RC2-04 / P0 si se exige dispositivos | Montar una vertical cliente de V1 que registre routers/switches/servidores, CPE principal e historial, sin convertirlos en aplicaciones | AA-D01/D02 opción de mantener MVP original; GAP-01/04 | Recorrido real del mismo dispositivo, versión desconocida, historial de identificaciones y CPE/CVE; AC originales aplicables |
| RC2-05 / P0 si no se difiere móvil | Cliente móvil mínimo online-first y conservación/reconciliación ante fallo | AA-D04 mantiene requisito histórico; GAP-02 | Registro móvil→API→consulta web del mismo activo; AC-A05/F08 en plataforma acordada, sin offline completo improvisado |
| RC2-06 / P1 condicionado | Historial de identificaciones CPE V2 o definición explícita del límite de selección actual | AA-D06 exige RF-04 completo en V2; GAP-04 | Contrato de actor/fecha/motivo/valores anteriores y concurrencia, API/UI y pruebas. D-002 coordina SQL/bootstrap/ORM/Alembic; no añadir tabla por copiar V1 sin diseño |
| RC2-07 / P1 condicionado | Política de workspace personal; o administración colaborativa si se exige | AA-D03 | Roles claros y aceptación de membresías solo si forman parte del alcance; matriz A/B/owner/editor/viewer y pool repetida |
| RC2-08 / P1 | Tipar respuestas V2 todavía permisivas y conciliar documentación actual/histórica | R-06/R-09 | OpenAPI/cliente/Postman consistentes; cambios compatibles o versión explícita; no borrar documentos antecedentes |
| RC2-09 / P1 | Revisar política de sesiones, expiración/logout y recuperación de acceso requerida | R-05; decisión funcional expresa | Requisitos de sesión medibles, prueba de tokens/recarga/logout; no guardar JWT en almacenamiento persistente como atajo |
| RC2-10 / P1 | Operación de backups y ensayo de recuperación acordado | R-04 y alcance de entrega | Copia externa cifrada si se exige operación; restore a volumen nuevo, retención/seguridad y evidencia sin secretos |
| RC2-11 / P2 | Auditoría de dependencias y avisos, navegadores/lectores acordados | R-07/R-08 | Evidencia vigente por herramienta, riesgo verificable y regresión; no afirmar vulnerabilidades por una deprecación |

Responsables y fechas se acordarán según capacidad; no se infieren de sus nombres
ni de las estimaciones históricas de cuatro semanas. Ambos pueden colaborar;
Database mantiene SQL/bootstrap/privilegios/pruebas y Backend ORM/Alembic según D-002.

## Funciones que continúan fuera o bloqueadas

- **ZAP/verificación/egreso operativo:** no son correcciones de RC1. Si se requieren
  para otra entrega, necesitan una misión independiente con autorización,
  plataforma/perímetro, controles DNS/TLS por conexión, revocación, límites y
  criterios de seguridad. Mantener bloqueos mientras falte cualquiera de esas
  condiciones. Las45/51 pruebas de laboratorio no bastan para habilitarlos.
- **NVD live:** solo si AA-D07 lo exige y se autoriza una prueba posterior de
  origen fijo, cuotas y presupuesto vigentes; nunca contacto a URL de inventario
  ni sustitución silenciosa de capturas. La aceptación fixture puede mantenerse.
- **Nueva arquitectura unificada de activos, migración automática V1→V2,
  scheduler, exportación PDF del producto, KEV/Redis, QR/GPS/cámara, offline
  completo, Active Scan o secretos de clientes:** no entran en RC2 por este plan.
  Cualquier ampliación necesita cambio de alcance explícito y su evaluación.

## Plan de QA para una RC2 que tenga cambios

1. Crear fuentes/entorno RC2 separados y registrar el conjunto exacto de commits.
   Conservar RC1 y development; no migrar datos personales experimentalmente.
2. Para cada defecto confirmado, reproducción, causa, criterio y cambio mínimo,
   prueba afectada y regresión. No relajar RLS, SSRF o semántica CVE para pasarla.
3. Ejecutar suites Backend/SQL en rutas nueva/existente con tmpfs, Frontend
   unitarias/componentes/tipos/lint/build, API/OpenAPI/Postman/Newman y navegador
   del dominio acordado. Mantener la cobertura RC1 y registrar conteos reales
   RC2; no fijar 755 como objetivo cosmético ni reciclar sus resultados.
4. Probar los 38 AC aplicables con trazabilidad individual, especialmente móvil,
   UI V1 o historial V2 si se incorporan. Separar lo no aplicable por decisión
   firmada de lo no probado o fallido. Repetir separación A/B y contexto de pool.
5. Reinicio, backup y restore en otro volumen; claves, DTO, revisión y aislamiento
   conservados. Actualizar matriz, defectos y manifiesto, con secretos excluidos.
6. Revisar los cambios y el checklist con ambos desarrolladores. Publicar/tag
   solamente bajo la autorización correspondiente, nunca fusionar ni desplegar
   automáticamente. Documentar si RC2 es otro candidato o la entrega académica
   definitiva; producción es una decisión y operación distintas.

**Criterio propuesto de RC2:** cumplir el alcance firmado y sus criterios, no
acumular funciones nuevas. Si únicamente se decide aceptar el piloto V2 con
límites explícitos, puede no requerirse implementar todos los trabajos
condicionales; esa exclusión debe quedar acordada, no supuesta.
