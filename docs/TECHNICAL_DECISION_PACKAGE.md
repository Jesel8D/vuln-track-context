# Paquete técnico: alternativas y recomendación

**PROPUESTA**, consulta de fuentes oficiales: 30-10-2026. No se aprobaron stack ni versiones. Hechos del proyecto: dos desarrolladores, cuatro semanas aproximadas, repositorios de implementación sin commits accesibles y primer flujo móvil → API → PostgreSQL → web. No hay datos sobre experiencia del equipo, plataforma móvil o presupuesto; esos datos pueden cambiar la recomendación.

| Dimensión | Python + FastAPI | Node.js + Express + TypeScript |
|---|---|---|
| Desarrollo y aprendizaje | FastAPI deriva validación y documentación de tipos y OpenAPI; ventaja si se conoce Python. Python y TypeScript implican dos lenguajes. | Mismo lenguaje que clientes reduce cambio de contexto; Express es minimalista y exige escoger y mantener validación, esquema OpenAPI y patrones de errores. |
| Contratos / pruebas | Pydantic y documentación OpenAPI facilitan contratos; tests de API y cliente NVD deben diseñarse. | Tipos TS no validan JSON en tiempo de ejecución: incorporar validador y pruebas; definir generación de contrato. |
| Seguridad / mantenimiento | Tipos no sustituyen autorización, límites o protección SSRF; trabajo pesado no debe bloquear API. | Las mismas obligaciones; middleware y control de errores deben diseñarse explícitamente. Ningún stack garantiza aislamiento ZAP. |
| Datos / integración | PostgreSQL con SQLAlchemy y Alembic; D-002 coloca sus modelos y migraciones en backend. | PostgreSQL con ORM/migraciones compatibles, por ejemplo Prisma; ubicación de herramientas alternativas requiere aclaración humana sin alterar D-002. |
| Costo de adopción | Menos infraestructura para describir API, pero requiere dominio del ecosistema Python. | Menos lenguajes, pero más decisiones de bibliotecas. Preferible si el equipo domina TS y no Python. |

Fuentes: [FastAPI](https://fastapi.tiangolo.com/features/), [Express](https://expressjs.com/en/guide/routing/), [SQLAlchemy](https://docs.sqlalchemy.org/en/20/tutorial/), [Alembic](https://alembic.sqlalchemy.org/en/latest/tutorial.html), [Prisma y migraciones](https://docs.prisma.io/docs/cli/migrate).

## Clientes y persistencia

- **PROPUESTA:** React + Vite + TypeScript para web: componentes y contratos tipados, con pruebas de accesibilidad y errores. Vite compila herramientas web; no resuelve identidad ni autorización. [React](https://react.dev/learn), [Vite](https://vite.dev/guide/).
- **PROPUESTA:** React Native + Expo + TypeScript para móvil: lenguaje compartido con web; requiere probar dispositivos, ciclo nativo y conservación del formulario. Expo Go no cubre toda configuración nativa; evaluar development build. Alternativa: React Native sin Expo si el equipo requiere control nativo que justifique mayor configuración; Flutter implicaría Dart y otro ecosistema, sin ventaja demostrada aquí. [React Native](https://reactnative.dev/docs/set-up-your-environment), [Expo development builds](https://docs.expo.dev/develop/development-builds/introduction/).
- **PROPUESTA:** PostgreSQL para persistencia transaccional y claves idempotentes; administrar configuración, scripts, seed y documentación en database conforme a D-002. No hay requisito aprobado que determine columnas o relaciones. [PostgreSQL](https://www.postgresql.org/docs/current/).
- **PROPUESTA:** Docker Compose para entornos locales reproducibles si el equipo puede operarlo; ejecución local sin contenedores reduce aprendizaje, pero aumenta variación de configuración. Una red Compose predeterminada comunica servicios y no es por sí sola aislamiento para ZAP; requieren controles de salida adicionales demostrados. [Docker Compose](https://docs.docker.com/compose/), [redes Compose](https://docs.docker.com/compose/how-tos/networking/).

## Recomendación y compatibilidad

**PROPUESTA principal:** FastAPI + Python, PostgreSQL + SQLAlchemy/Alembic, React/Vite/TypeScript, React Native/Expo/TypeScript y Compose para laboratorio, condicionado a que el equipo conozca Python y pueda demostrar el entorno. Es coherente con los borradores existentes y reduce trabajo de contrato OpenAPI; no es una decisión aprobada. **Alternativa:** Express/TypeScript y PostgreSQL con ORM/migraciones compatibles si el equipo domina TypeScript; añadir explícitamente validación de entrada, OpenAPI, pruebas y estrategia de migraciones. La ubicación de modelos y migraciones alternativas es **PENDIENTE**; D-002 no se modifica.

**Versiones observadas, no recomendadas ni probadas como conjunto:** la página oficial [Node.js](https://nodejs.org/en/about/previous-releases) identifica líneas 22, 24 y 26 como LTS en la fecha de consulta; [Express](https://expressjs.com/en/guide/migrating-5/) indica Node 18 o superior para Express 5, pero el mínimo compatible no equivale a soporte vigente. [PostgreSQL](https://www.postgresql.org/docs/current/) muestra versión 18 en documentación actual. Se verificaron afirmaciones individuales, no la compatibilidad entre Expo, Node, React, Vite, Python, drivers y demás dependencias. **PENDIENTE:** escoger y verificar matriz exacta, soporte, dispositivos y versiones bloqueadas tras una prueba reproducible; no se recomiendan números concretos sin ella.

## Operación

**PROPUESTA:** desarrollo local y pruebas aisladas con datos sintéticos; CI y entorno de demostración solo tras aprobación; evitar despliegue de producción como supuesto. Confirmar coste, accesibilidad del dispositivo, instalación de herramientas y responsabilidades. Los argumentos son recomendaciones, no garantías de entrega en cuatro semanas. Ver [revisión del stack anterior](STACK_EVALUATION.md).
