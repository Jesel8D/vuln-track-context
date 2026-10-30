# Criterios de aceptación y casos de uso

Las reglas señaladas ESTABLECIDA provienen del alcance; los escenarios, validaciones y comportamientos concretos son **PROPUESTA** para revisión. Credenciales, persistencia local, catálogo de errores y plazos siguen **PENDIENTE**. Los datos de prueba deben ser sintéticos y ZAP solo debe usarse en un entorno de prueba autorizado.

## AC-A — Registro móvil

- **Actor:** Operador de campo
- **Precondiciones:** Formulario disponible; autorización según decisión pendiente.
- **Flujo principal:** Captura tipo, fabricante, modelo, versión y sitio; añade opcionales y envía.
- **Flujos alternativos:** Si faltan datos, informa qué falta sin borrar el formulario.
- **Resultado esperado:** Solicitud de registro con datos introducidos.
- **Reglas:** Campos contemplados y opcionales son ESTABLECIDA; longitudes y catálogos son PROPUESTA.
- **Criterio Given / When / Then:** Given formulario completo, When se envía, Then se solicitan alta y confirmación.
- **Prueba negativa PROPUESTA:** Enviar campo obligatorio vacío: rechazo propuesto sin pérdida de datos.

## AC-B — Conservación y reintento

- **Actor:** Operador de campo
- **Precondiciones:** Hay datos en el formulario.
- **Flujo principal:** Envío falla por conectividad; se muestra el problema; el usuario reintenta.
- **Flujos alternativos:** Si la respuesta se pierde tras guardar, reutilizar clave de idempotencia propuesta.
- **Resultado esperado:** Datos preservados y posibilidad de reintentar.
- **Reglas:** Conservar tras fallo es ESTABLECIDA; duración y persistencia al cerrar app PENDIENTE.
- **Criterio Given / When / Then:** Given envío fallido, When se vuelve al formulario, Then todos los valores siguen disponibles y se puede reintentar.
- **Prueba negativa PROPUESTA:** Sin red repetidamente: no vaciar ni presentar alta confirmada.

## AC-C — Recepción y validación API

- **Actor:** Cliente móvil
- **Precondiciones:** API disponible; autorización y esquema aprobados antes de implementar.
- **Flujo principal:** Recibe y valida estructura y campos; devuelve resultado.
- **Flujos alternativos:** Datos inválidos producen error de campo sin registrar.
- **Resultado esperado:** Solicitud válida aceptada para persistir.
- **Reglas:** Recepción ESTABLECIDA; contrato y validaciones PROPUESTA.
- **Criterio Given / When / Then:** Given solicitud válida, When llega a API, Then se valida y se inicia persistencia.
- **Prueba negativa PROPUESTA:** JSON malformado, campo desconocido o no autorizado: rechazar según contrato propuesto.

## AC-D — Persistencia

- **Actor:** Backend
- **Precondiciones:** Datos validados y conexión disponible.
- **Flujo principal:** Persiste transaccionalmente y confirma solo tras guardar.
- **Flujos alternativos:** Error de base de datos no devuelve éxito; mismo reintento no duplica bajo política propuesta.
- **Resultado esperado:** Activo consultable por identificador.
- **Reglas:** Persistencia ESTABLECIDA; transacción e idempotencia PROPUESTA.
- **Criterio Given / When / Then:** Given activo válido, When se confirma almacenamiento, Then lectura devuelve el mismo registro.
- **Prueba negativa PROPUESTA:** Fallo antes de commit: ningún éxito; pérdida de respuesta tras commit: no duplicar con misma clave propuesta.

## AC-E — Consulta web

- **Actor:** Usuario web
- **Precondiciones:** Activo confirmado; acceso autorizado según decisión pendiente.
- **Flujo principal:** Abre listado y consulta registro.
- **Flujos alternativos:** Si no existe o acceso denegado, mostrar respuesta apropiada sin revelar información.
- **Resultado esperado:** Visualización de datos del activo.
- **Reglas:** Consulta ESTABLECIDA; paginación y permisos PROPUESTA.
- **Criterio Given / When / Then:** Given activo persistido, When usuario autorizado consulta, Then ve sus datos.
- **Prueba negativa PROPUESTA:** Recurso inexistente o no autorizado no revela datos.

## AC-F — Búsqueda de CPE

- **Actor:** Analista
- **Precondiciones:** Consulta de producto disponible.
- **Flujo principal:** Busca candidatos y revisa nombres, versión y obsolescencia.
- **Flujos alternativos:** Sin resultados informar vacío; error externo se distingue.
- **Resultado esperado:** Lista de candidatos sin selección automática.
- **Reglas:** Búsqueda asistida ESTABLECIDA; paginación PROPUESTA.
- **Criterio Given / When / Then:** Given búsqueda válida, When NVD responde, Then se muestran candidatos o resultado vacío.
- **Prueba negativa PROPUESTA:** Respuesta externa fallida no se presenta como lista vacía definitiva.

## AC-G — Selección manual CPE

- **Actor:** Analista
- **Precondiciones:** Se muestran candidatos o ninguno.
- **Flujo principal:** Compara candidato y lo selecciona explícitamente.
- **Flujos alternativos:** Si ninguno corresponde, no selecciona ni consulta.
- **Resultado esperado:** CPE elegido asociado a la consulta propuesta.
- **Reglas:** Selección humana ESTABLECIDA; modo de guardar PENDIENTE.
- **Criterio Given / When / Then:** Given candidatos, When persona selecciona uno, Then la consulta usa ese CPE.
- **Prueba negativa PROPUESTA:** Sin selección o CPE incompleto para filtro NVD: no iniciar consulta y explicar.

## AC-H — Consulta CVE NVD

- **Actor:** Analista
- **Precondiciones:** CPE seleccionado, elegible para filtro de NVD.
- **Flujo principal:** Solicita consulta; backend obtiene páginas necesarias.
- **Flujos alternativos:** Tiempo agotado o páginas faltantes se identifican; se puede reintentar.
- **Resultado esperado:** CVE y momento/cobertura de consulta.
- **Reglas:** Sin espejo completo ESTABLECIDA; tiempos y almacenamiento PROPUESTA.
- **Criterio Given / When / Then:** Given CPE válido, When NVD responde completamente, Then se presentan CVE recuperados.
- **Prueba negativa PROPUESTA:** Ante fallo o respuesta parcial no afirmar ausencia de CVE.

## AC-I — Presentación de hallazgos

- **Actor:** Analista
- **Precondiciones:** Hay respuesta de consulta o alerta de ZAP.
- **Flujo principal:** Consulta resultados con fuente y fecha.
- **Flujos alternativos:** Sin métricas CVSS, muestra sin puntuación; datos incompletos se indican.
- **Resultado esperado:** CVE marcados como potencialmente aplicables hasta verificación.
- **Reglas:** Carácter potencial ESTABLECIDA; clasificación de ZAP y ciclo PROPUESTA.
- **Criterio Given / When / Then:** Given CVE asociado al CPE, When se muestra, Then se identifica como potencial hasta verificar.
- **Prueba negativa PROPUESTA:** No rotular CVE como confirmado ni valor ausente como cero.

## AC-J — Ejecución Baseline

- **Actor:** Analista autorizado
- **Precondiciones:** Destino web de prueba autorizado, en lista y controles de salida comprobados.
- **Flujo principal:** Solicita por identificador; backend valida, ejecuta Baseline aislado y permite consultar resultado.
- **Flujos alternativos:** Cola, fallo, tiempo agotado o alertas se distinguen.
- **Resultado esperado:** Ejecución y alertas accesibles a quien tenga permiso.
- **Reglas:** Solo Baseline ESTABLECIDA; estados, idempotencia y tiempo PROPUESTA.
- **Criterio Given / When / Then:** Given destino permitido y autorización, When solicita Baseline, Then puede consultar progreso y resultado.
- **Prueba negativa PROPUESTA:** Si el aislamiento no puede verificarse, no iniciar; códigos de alerta no equivalen a fallo técnico.

## AC-K — Rechazo de destinos

- **Actor:** Analista o atacante
- **Precondiciones:** Lista controlada y frontera de salida verificadas.
- **Flujo principal:** Solicita destino permitido; se valida toda conexión y redirección.
- **Flujos alternativos:** Destino revocado, DNS cambiado o redirección fuera de alcance se bloquea.
- **Resultado esperado:** No se conecta a objetivo no autorizado.
- **Reglas:** Lista, autorización y no Active Scan ESTABLECIDA; mecanismo de control PROPUESTA.
- **Criterio Given / When / Then:** Given destino fuera de lista, When se solicita o se intenta alcanzar por redirección, Then se bloquea sin conexión.
- **Prueba negativa PROPUESTA:** Probar con simulación controlada DNS rebinding, IPv6, redirección y puertos fuera de alcance.

## AC-L — Error de integración externa

- **Actor:** Analista
- **Precondiciones:** Consulta NVD o análisis ZAP solicitado.
- **Flujo principal:** Se identifica fallo externo y conserva lo ya confirmado.
- **Flujos alternativos:** Agotamiento de tiempo, tasa o reporte malformado generan diagnóstico seguro y opción de reintento según política.
- **Resultado esperado:** Error no se confunde con lista vacía o resultado exitoso.
- **Reglas:** Diferenciación y reintento acotado PROPUESTA; límites concretos PENDIENTE.
- **Criterio Given / When / Then:** Given integración no disponible, When se agota tiempo propuesto, Then se informa error y no se inventan resultados.
- **Prueba negativa PROPUESTA:** Probar error 5xx, límite, respuesta incompleta y reinicio durante ejecución.
