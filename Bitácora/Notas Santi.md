# Notas Santi

Bitácora del proyecto, de la más reciente a la más vieja. Las fechas de commits salen del historial de `sedici/dARK` (rama `dev-3_4`) y del core. Para el estado consolidado ver [[Estado del proyecto]].

## Foco actual (semana 6)
- Cerrar dudas con LA Referencia ([[Pendientes y dudas abiertas]]) y arrancar la Entrega 3.
- Mis tareas de la E3: **E3-T1** (catálogo del core), **E3-T2** (DTOs), **E3-T4** (asignación individual y masiva) y **E3-T5** (estado y sincronización).
- Relevar en 3.3 y 3.5 los hooks de aceptación, publicación y despublicación ([[Wrappers OJS 3.3, 3.4 y 3.5#Para la Entrega 3 (a relevar)]]).

---

### 2026-11-04: reunión con LA Referencia
- Presentación del estado del proyecto y de las dudas. Ver [[2026-11-04 Reunión con LA Referencia]].
- _Anotar acá lo más importante que salga de la reunión._

### 2026-11-03: preparación de la reunión
- Presentación de 24 diapositivas con capturas de OJS 3.4 en el sandbox (`capturas-4_11/`).
- Revisamos el código del minter (copia de `dark-deployer` del 24/09) y `dark-core-lib` (público, 31/10). Hallazgos:
  - La autenticación pasó a firma **Ed25519** en `dark-core-lib`; la «API key» no existe en el minter.
  - El minter no tiene campo para el OAI identifier.
  - Hay dos formatos de error, y el 409 es ambiguo.
  - El tombstone no se propaga a la blockchain.
  - El dígito de control NOID afecta la carga manual.
- Documento `Dudas reunión 4_11.md`.

### 2026-11-01 / 02: limpieza post Entrega 2
- `CsvRecord` inmutable (`withImportResult()`), PRs #37 y #46.
- Se eliminó código muerto y duplicado (PR #42). Reglas de asignación, estados e importación delegadas al core (`ManualAssignment::edit()`, búsqueda de artículos como callable).
- Aviso del ARK en el paso de publicación; el import CSV registra directo.

### 2026-10-30: Entrega 2 integrada
- Importación CSV en Herramientas → Importar/Exportar (E2-T3), PR #34, en las tres ramas. Ver [[Importación por CSV]].
- Reporte descargable, límite de 5 MB y 2000 filas, y la aplicación delegada a `ImportService::apply()`.

### 2026-10-28: tags de la Entrega 1
- `e1-ojs3.3`, `e1-ojs3.4`, `e1-ojs3.5` y `e1` en el core.
- Submódulo apuntando a `stable-php-7.4`.

### 2026-10-22: arreglos de la 3.4
- Clave de traducción para el placeholder de la API key (PR #29).

### 2026-10-14: arreglos críticos (PR #22)
- API key enmascarada en el formulario, sin filtrarse en el HTML.
- Barra final opcional en `resolverUrl`; validación de prefijo sin distinguir mayúsculas.
- El prefijo se revalida solo si cambia el ARK; `isExported` es de solo lectura.
- `onPublicationEdit` no pisa el estado en los guardados programáticos.
- `TypeError` con `submissionId` null.

### 2026-10-12: delegación al core
- `JournalConfigAdapter`; validaciones de `DARKSettingsForm` delegadas al core (PR #14).
- `getResolvingURL` → `buildResolverUrl`; validación de prefijo → `matchesPrefix` (PR #17).

### 2026-10-11: almacenamiento del ARK
- E1-T4 (PR #11): ARK en `pub-id::other::dark` más los campos `dark::*`.
- Se quita la opción `isPublicExposed`; habría que reponerla si se decide ocultar los ARK en el piloto.

### 2026-10-08: ajustes de la revista
- E1-T3 (PR #8): formulario de configuración por revista.

### 2026-10-03 / 06: core
- Se agrega dARKCore como submódulo (03/10).
- CI con GitHub Actions para los tests del core (06/10). Ver [[Notas Lauti#Sobre los tests de dARKCore]].

### 2026-10-05: sandbox
- `ojs-sandbox` con OJS 3.3, 3.4 y 3.5, MySQL 8 y worktrees (E0-T2). Ver [[¿Cómo usar?|Sandbox]].

### 2026-09-28 / 29: inicio
- Plan de trabajo (28/09) y repo del plugin (`sedici/dARK`, primer commit el 29/09).
- Plan de implementación rev. 2 (01/10): core y wrappers, DTOs, reparto de tareas. Ver [[Plan de implementación]].
