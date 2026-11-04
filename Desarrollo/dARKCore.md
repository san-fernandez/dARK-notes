Librería PHP (`sedici/dark-core`, namespace `DARKCore\`, PSR-4 en `src/`) con la lógica de dARK que no depende de OJS. **Requiere PHP ≥ 7.4.** Ojo con OJS 3.3; ver [[Pendientes y dudas abiertas#10. Decisiones internas pendientes (equipo)]].

El wrapper la carga con `bootstrap.php`: usa `vendor/autoload.php` si existe y, si no, un autoloader propio sobre `dARKCore/src/`.

## Clases de servicio

| Clase | Qué hace |
|---|---|
| `dARKCore` | `validateArkFormat()`, `normalizeArk()` (→ `ark:NAAN/nombre`), `tryNormalizeArk()`, `buildArk(prefijo, sufijo)`, `arkVariants()` (forma compacta y forma con barra), `buildResolverUrl(config, ark)`, `matchesPrefix(ark, prefijo)` |
| `JournalConfigValidator` | UUID v4, prefijo (`NAAN` de 5–9 dígitos con `/shoulder` opcional), API key no vacía, URL del resolver (**solo dominio**, sin path, query ni credenciales), momento `pre_publication` o `post_publication` |
| `ManualAssignment` | `validate(ark, actual, prefijo, isTaken)` aplica, en orden, formato → prefijo (solo si el ARK cambió) → unicidad (callable que aporta el wrapper). `edit()` devuelve un `ArkEdit` con el ARK normalizado y el nuevo `ArkRecord` |
| `ImportService` | `validateFile()`, `analyze(CsvDocument, findArticles, config)`, `apply(records, save)`, `summarize()`, `toCsv()` (reporte). Ver [[Importación por CSV]] |
| `CsvValidationContext` | Contexto tipado para validar las filas: duplicados en el archivo, existencia del OAI, ARK ya tomado |

Formato aceptado por `validateArkFormat`: `ark:[/]NAAN/nombre`, con NAAN de 5–9 dígitos, sin distinguir mayúsculas.

## DTOs

| DTO | Entrega | Contenido |
|---|---|---|
| `JournalConfig` | E1 | `journalUuid`, `arkPrefix`, `apiKey`, `resolverUrl`, `assignmentMoment` |
| `ArkRecord` | E1 | `ark`, `status` (`unassigned`, `manual_assigned`, `reserved`, `registered`, `error`), `assignedAt`, `lastError`, `isExported`. Factories `manualAssignment()`, `unassigned()`, `afterManualEdit()` |
| `ArkEdit` | E1 | ARK normalizado y `ArkRecord` resultante (o null si no cambió) |
| `Article` | E1 | submission y publication id, OAI, URL pública, estado (`queued`, `published`, `declined`), fecha. **Todavía no se usa** |
| `ApiResult` | E1 | Éxito, HTTP status, datos, código y tipo de error (`none`, `transient`, `permanent`). **Todavía no se usa** |
| `CsvDocument` | E2 | Contenido, nombre y tamaño |
| `CsvRecord` | E2 | Fila, OAI, ARK, error de validación, resultado de importación (`not_imported_yet`, `saved`, `failed`, `skipped`). Inmutable (`withImportResult()`) |
| `ArticleOaiArk` | E2 | articleId, OAI y ARK actual |
| `ImportAnalysis` | E2 | Errores del parser y filas |
| `ImportSummary` | E2 | Totales: total, válidas, con errores, guardadas, omitidas y fallidas |
| `ParserError` | E2 | Errores del archivo (cabecera, codificación, separador, vacío, extensión, tamaño, filas) |

**Faltan** (E3–E5): DTOs de la solicitud de reserva y registro, `EditorialEvent` y `OperationRecord`. Ver [[Deuda técnica#dARKCore]].

## Tests y CI
- PHPUnit 9.5. Se corren con `composer test`. Hay **118 tests** entre `tests/` y `tests/DTO/`.
- GitHub Actions (`.github/workflows/tests.yml`): matriz con PHP **7.4, 8.0, 8.2 y 8.3**. Corre en push a `dev-php-7.4`, en PRs a `dev-php-7.4` y `stable-php-7.4`, y a mano.
- Cómo funciona PHPUnit: ver [[Notas Lauti#Sobre los tests de dARKCore]].

## Decisiones de diseño
- El core **no accede a OJS**: lo que necesita la base de datos (unicidad, buscar artículos por OAI, guardar) lo recibe como **callable**.
- El HTTP hacia dARK se va a hacer con **cURL**, sin depender del cliente de cada versión de OJS.
- Forma canónica del ARK: **compacta** `ark:NAAN/nombre`, alineada con `dark-core-lib` desde el 31/10. Para buscar valores viejos se usan las dos variantes (`arkVariants()`).
