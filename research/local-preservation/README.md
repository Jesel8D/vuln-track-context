# Preservación de cambio local pendiente

Parche exacto del cambio preexistente de `database/diagrams/database_v1.mmd`, sobre Database V1 b493fd17e9290ca94fa085d49230073ed1837e1a. Elimina `username` del diagrama; la columna SQL permanece. Intención ambigua: no se aprobó ni se aplicó a ninguna rama Database. El archivo original permanece modificado en su equipo de origen.

Para recuperar únicamente en una copia local de V1 b493fd1: revisar el parche y ejecutar `git apply --check /ruta/al/parche`; aplicar solo tras confirmar intención. No aplicarlo a V2 ni mezclarlo con auditoría. El diff respaldado permite reconstruir el archivo sin descartar su cambio. SHA-256 original modificado: `1bd797be050bca20fe07b1d8f158ada384703e11fc17f8eaf8ed16ebdbaba590`.
