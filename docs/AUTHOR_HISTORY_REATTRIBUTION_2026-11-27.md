# Reasignación de commits Codex — 27-11-2026

## Alcance

Por autorización explícita del usuario solicitante, se reasignó la identidad de commits de `main` de Backend y Frontend cuando autor o committer era Codex (`Codex <codex@users.noreply.github.com>`). La nueva identidad es `RudiFabricio8 <243763@ids.upchiapas.edu.mx>`. El cambio afecta los metadatos Git y por tanto los hashes; no modifica los archivos de los commits.

No se reescribieron Database ni Context porque sus commits existentes no tenían identidad Codex. Tampoco se reescribieron ramas de trabajo o release. Los metadatos de autores/committers ajenos a Codex se conservaron; sus hashes pueden cambiar si dependen de un commit reescrito.

## Evidencia de preservación

Antes de reescribir se crearon tags anotados locales que señalan los commits originales. Comparación del árbol Git completo anterior y posterior:

| Repositorio | `main` original | Tag de respaldo | `main` local reescrito | Árbol antes/después |
|---|---|---|---|---|
| Backend | `df60cea165c67d85374beebbff75c752cc483d61` | `pre-rudi-author-rewrite-20261127` | `bfff694066a1dbfe6d714e2cd1cec4476054eb0f` | `b681ac605768d977cba85ebfd43e14a8376e2a64` = `b681ac605768d977cba85ebfd43e14a8376e2a64` |
| Frontend | `80060c0c82b236e7f6d67ec2dfeaa705ae90f90d` | `pre-rudi-author-rewrite-20261127` | `4db3818c91c7dc429ebc2cc2832faa818e97cb29` | `6ef918e9d3bc328508cb94699d4618892383ae23` = `6ef918e9d3bc328508cb94699d4618892383ae23` |

Los tags locales protegen la historia original y pueden publicarse para mantenerla recuperable en GitHub. El recuento inicial de entradas etiquetadas con Codex fue 51 en Backend y 83 en Frontend; tras el cambio, el recuento de identidad Codex en los historiales locales de `main` es cero. La igualdad de tree SHA verifica que el contenido en cada punta no cambió durante la reasignación.

## Sincronización remota pendiente

El intento de `git fetch origin main` falló por falta de conexión a `github.com:443`. Por ello, los tags de respaldo y los nuevos hashes aún existen solo en los clones locales. Para publicar el resultado cuando GitHub sea accesible, primero actualizar/verificar que `origin/main` siga en los hashes originales indicados; después subir el tag y actualizar `main` con `--force-with-lease` condicionado al hash original. Si el remoto avanzó, detenerse y reconciliar antes de forzar nada.

Al cambiar historia, quienes ya tengan clones deberán sincronizarlos con `origin/main` actualizado; el tag de respaldo conserva el historial previo. No aplicar `git pull` normal sobre un clon que conserve la rama antigua.
