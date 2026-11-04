# Wrappers OJS 3.3, 3.4 y 3.5

Cada versión de OJS tiene su rama (`dev-3_x`) y su worktree (`dark-worktrees/ojs3x`). La estructura es la misma en las tres; cambian las convenciones de cada versión.

## Estructura de un wrapper
```text
DARKPubIdPlugin.php (.inc.php en 3.3)   clase principal (pubIds)
bootstrap.php                           autoloader de dARKCore
classes/
  ArkRecordAdapter                      ArkRecord ⇄ datos de la publicación
  JournalConfigAdapter                  plugin_settings → JournalConfig
  CsvDocumentAdapter                    TemporaryFile → CsvDocument
  form/DARKSettingsForm                 ajustes de la revista
  importexport/DARKImportExportPlugin   subplugin de importación CSV
  importexport/ArticleProvider          OAI ⇄ submissionId, ARK actuales (query builder)
templates/   settingsForm, darkAssign, darkSuffixEdit, importexport/{index,preview,complete}
locale/      es, en, pt_BR
test-data/   CSV de prueba (válido, BOM con ;, cabecera inválida, vacío, errores…)
dARKCore/    submódulo
```

## Hooks que usa el plugin

| Hook | Para qué |
|---|---|
| `Publication::validate` | Valida el ARK cargado (formato, prefijo, unicidad) e impide modificar los campos internos `dark::*` desde la API REST |
| `Form::config::before` (formulario de identificadores) | Agrega el campo del ARK a la pestaña Identificadores |
| `Form::config::before` (formulario `publish`) | Aviso en el paso de publicación: «se publica con ARK X» o «sin ARK» |
| `Publication::edit` | Recalcula el `ArkRecord` cuando el editor cambia el ARK, sin pisar el estado en los guardados programáticos |

Registro de hooks: `HookRegistry::register(...)` en 3.3, `Hook::add(..., [$this, 'm'])` en 3.4 y `Hook::add(..., $this->m(...))` en 3.5.

## Persistencia
En la publicación (`publication_settings`):
- `pub-id::other::dark`: el ARK, normalizado.
- `dark::status`, `dark::assignedAt`, `dark::lastError` y `dark::isExported`: campos internos (`INTERNAL_FIELDS`), agregados al schema en `addToSchema()`.
  - No se marcan `readOnly` porque OJS descarta esas propiedades al sanitizar. La protección la hace `validatePublicationDark()`.

Ajustes de la revista (`plugin_settings`): `journalUuid`, `darkPrefix`, `apiKey`, `resolverUrl` y `assignmentMoment`.

**OAI identifier:** `oai:<repository_id>:article/<submissionId>`, el mismo formato que `JournalOAI`. `repository_id` sale de `[oai]` en `config.inc.php`; si no está, se usa el host. `submission_id` no cambia desde el envío, así que el OAI **se puede predecir antes de publicar**.

## Diferencias entre versiones que nos afectan
Del plan de implementación, sección 2.

| Aspecto | 3.3 | 3.4 | 3.5 |
|---|---|---|---|
| PHP mínimo de OJS | 7.3 (**el core pide 7.4**) | 8.0 | 8.2 |
| PHP del sandbox | 7.4 | 8.0 | 8.2 |
| Clases | `.inc.php`, `import()`, sin namespaces | `.php` con namespace `APP\plugins\…` | Solo namespaces |
| Hooks | `HookRegistry` | `Hook::add` | `Hook::add`; varios hooks eliminados |
| Eventos de Laravel | No | Sí, pocos | Sí |
| Datos | DAOs | Facades `Repo` | `Repo` y Eloquent |
| Tareas programadas | `scheduledTasks.xml` y cron o Acron | Igual | **Laravel Scheduler**; no hay XML ni Acron |
| Colas y jobs | No | Sí | Sí |
| Interfaz | Smarty y Vue en partes | Vue 2 (`pkp.Vue.compile`) | **Vue 3**; Smarty deprecado en el editorial |
| Locales | `en_US` | `en` | `en` |
| API REST | Slim | Slim | Rutas de Laravel |

**Consecuencia:** 3.3 es la más distinta en PHP y 3.5 en la interfaz. Por eso Santiago lleva 3.3 y 3.5, y Lautaro el core y 3.4.

## Para la Entrega 3 (a relevar)
- [ ] Hook o evento de **aceptación** (decisión editorial) y de **paso a producción** en cada versión, para la reserva previa.
- [ ] Hook de **publicación** y de **despublicación** (`Publication::publish`, `Publication::unpublish`) en 3.3, 3.4 y 3.5.
- [ ] Creación de una **nueva versión**.
- [ ] Mecanismo de tarea periódica en cada versión. El sandbox tiene `make cron-33` y `make cron-35`.
- [ ] Cliente HTTP: cURL en el core (decidido).
