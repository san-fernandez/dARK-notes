# Configuración, asignación y exposición (Entrega 1)

Estado: hecha en 3.3, 3.4 y 3.5 (tags `e1-*`, 28/10). Las capturas están en `capturas-4_11/` (01–14 y 30–32).

## Instalación
Ajustes del sitio web → Módulos → buscar «dARK». Se instala una sola vez y aparece en dos lugares: como **identificador público** (dARK) y como **módulo de importación** (CSV).

## Ajustes de la revista

| Campo | Validación (core) | Nota |
|---|---|---|
| UUID de la revista | UUID v4 | Lo emite LA Referencia; es el `authority_id` |
| Prefijo ARK | `NAAN` de 5–9 dígitos, o `NAAN/shoulder` | ¿Existen shoulders por revista? (pendiente) |
| API key | No vacía; **solo escritura**: no vuelve a mostrarse | Probablemente haya que reemplazarla por la clave de firma Ed25519 |
| URL del resolver | Solo dominio http(s), sin path | El enlace queda `https://dominio/ark:12345/x` |
| Momento de asignación | `pre_publication` o `post_publication` | Se va a usar en la E3 |

Las credenciales viven en `plugin_settings`, asociadas al `context_id`. La API key no debe aparecer en la API REST, en OAI, en los metadatos públicos ni en los logs.

## Asignación manual
- Publicación → **Identificadores**: el editor carga el ARK a mano.
- Se rechaza si tiene **formato inválido**, **otro prefijo** o si **ya lo usa otro artículo** de la revista. El prefijo se controla solo si el ARK cambió, para no trabar artículos viejos después de un cambio de prefijo.
- Se guarda normalizado; el estado queda en `manual_assigned`. Si se borra, vuelve a `unassigned`.
- En el **paso de publicar**: si hay ARK, muestra con cuál se publica; si no hay, muestra un aviso **pero deja publicar**.

## Exposición
- **Página del artículo:** enlace al resolver configurado, solo si el artículo tiene ARK.
- **Metadatos HTML:** `DC.Identifier.dARK`, que OJS genera solo por ser pubIds, y `citation_dark`, que agrega el plugin de Google Scholar.
- **OAI-PMH (`oai_dc`):** `dc:identifier` = `ark:12345/art2`, además de la URL del artículo.

## Pendiente de definir
Ver [[Pendientes y dudas abiertas#3. Asignación manual en la publicación]] y [[Pendientes y dudas abiertas#4. Exposición del ARK]]:
- `DC.Identifier.dARK` o `DC.Identifier.ark`; si se deja `citation_dark`; ARK o URL en OAI.
- Si se mantiene la carga manual, dado el dígito de control NOID del minter.
- Si se oculta la exposición durante el piloto.
- Qué pasa al cambiar o borrar un ARK ya registrado en dARK.
