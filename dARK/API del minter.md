# API del minter

Lo que relevamos del código del minter para la Entrega 3. **El minter puede haber cambiado**: nuestra copia de `dark-deployer` es del 24/09 y el repo dejó de estar visible. Las dudas están en [[Pendientes y dudas abiertas#6. API del minter y metadatos]].

## Endpoints

| Operación | Endpoint | Datos y notas |
|---|---|---|
| Reservar | `POST /api/v1/arks` | `authority_id`, `naan` → **201**, estado R. `extra=forbid`: un campo de más devuelve 422 |
| Reservar en lote | `POST /api/v1/arks/batch` | `authority_id`, `naan`, `items[client_item_id]`. Sin tope en el código |
| Registrar o actualizar | `PUT /api/v1/arks/{ark}` | `authority_id`, `target`, `minimal_metadata` (L1), `original_metadata` (L2), `metadata_schema`, `metadata_media_type`. R→D o P→U |
| Consultar | `GET /api/v1/arks/{ark}` | Requiere mTLS. Devuelve `ARKResponse` |
| Consultar en lote | `POST /api/v1/arks/status/batch` | Hasta 100 ARK. **No figura en la documentación** |
| Tombstone | `DELETE /api/v1/arks/{ark}` | Irreversible. Hoy no se propaga a la blockchain |
| Validar autoridad | `GET /api/v1/authority/{uuid}/authorized/{naan}` | Requiere mTLS. Candidato para validar los ajustes |

`ARKResponse`: `ark`, `state`, `target`, `metadata_cid`, `metadata_schema`, `minimal_metadata`, `level1_cid`, `level2_cid` y `client_item_id`. Todo es opcional salvo `ark` y `state`.

## Autenticación
Hay **tres mecanismos en juego**, y no está definido cuál va a usar OJS:

1. **mTLS:** según la documentación, las escrituras requieren un certificado cuyo CN sea el UUID de la autoridad, y las lecturas requieren mTLS. Es lo que hace nuestra copia del minter.
2. **Header `X-Authority-Id`:** también en nuestra copia del minter.
3. **Firma Ed25519** (en `dark-core-lib` desde el 13/10, `request_signing.py`). La revista firma con su clave privada y LA Referencia registra la clave pública de la autoridad en la blockchain (`set_authority_client_key`, solo el administrador). Headers:
   - `X-DARK-Authority-Id`
   - `X-DARK-Key-Id`
   - `X-DARK-Timestamp` (tolerancia propuesta de 300 s)
   - `X-DARK-Nonce`
   - `X-DARK-Content-SHA256`
   - `X-DARK-Signature`

En PHP se puede firmar con la extensión **sodium** (desde PHP 7.2), así que sirve para las tres versiones de OJS.

> [!warning] La «API key» que pide hoy el plugin no existe en el código del minter.

## Metadatos L1 y L2
**L1 = `minimal_metadata`** (17 campos en total):

| Grupo | Campos | Quién los completa |
|---|---|---|
| Obligatorios | `title`, `authors` (lista), `year` (entero) | OJS |
| Opcionales | `publisher`, `resource_type`, `language`, `abstract`, `subjects`, `rights`, `alternate_identifiers`, `alternate_urls` | OJS, si tiene el dato |
| De sistema | `original_metadata {schema, media_type}`, `ark`, `$schema`, `schema_version`, `created_at`, `updated_at` | El servidor |

Fuente: `dark-core-lib`, `metadata/schemas.py` (`Level1Metadata`). La documentación de la API muestra `creator` y `date`, y no coincide con el código.

**L2 = `original_metadata`**: el registro original completo, sin transformar. `metadata_schema` acepta `dublin_core`, `oai_dc`, `datacite`, `openaire4`, `jats` y `other`. **Propuesta:** enviar el `oai_dc` que ya genera OJS.

**OAI identifier:** el minter no tiene un campo para él. Candidato: `alternate_identifiers` (`{schema, value}`).

## Forma del ARK
- Desde el 31/10, `dark-core-lib` toma como canónica la forma compacta `ark:12345/x` y acepta también `ark:/12345/x`.
- La documentación del minter y del resolver todavía muestra `ark:/12345/abc123`.
- El plugin guarda la forma compacta. Ver [[dARKCore]].
- El minter valida el **dígito de control NOID** (`MINTER_NOID_CHECKDIGIT`, activo por defecto) al actualizar y al hacer tombstone.

## Errores
Hay dos formatos:
- Los errores de la librería devuelven `{error, message, retryable, details}` con los headers `X-DARK-Error-Code` y `X-DARK-Retryable` (`exceptions/handlers.py`).
- Los `HTTPException` de `api/arks.py` devuelven `{detail}` sin headers (409 por tombstone o por operación pendiente, 400 por dígito de control, 404).

| Código | Significado |
|---|---|
| 400 | Formato inválido o dígito de control |
| 401 | Credencial ausente o inválida |
| 403 | La autoridad no está autorizada para ese NAAN o shoulder |
| 404 | El ARK no existe ni en la base ni en la blockchain |
| 409 | Tombstone, creación o actualización pendiente, **o** en proceso o colisión al reservar |
| 422 | El body no cumple el esquema (campo faltante o extra, tipo incorrecto, lista vacía) |
| 500 | Error interno |
| 502 | Falla al leer la blockchain |
| 503 | Servicio no disponible |

## Política de reintentos (propuesta, sin confirmar)
- **Reintentar** con backoff exponencial: 500, 502, 503 y el 409 «en proceso».
- **No reintentar:** 400, 401, 403, 404, 422 y el 409 por tombstone o por operación pendiente.
- Distinguir los 409 por `X-DARK-Error-Code`, si se unifica el formato de error.
- En el core, `ApiResult` ya clasifica los resultados en `transient` o `permanent`. Ver [[dARKCore]].
