# Deuda técnica y pendientes de código

Cosas del **código** que quedaron pendientes o a medio camino. Las dudas de definición están en [[Pendientes y dudas abiertas]].

## Repositorios y versiones
- [ ] **Entrega 2 sin etiquetar.** Hay tags `e1-ojs3.3`, `e1-ojs3.4`, `e1-ojs3.5` y `e1` (core), pero ninguno `e2`. Las ramas `dev-3_x` van unos 19–20 commits por delante de `stable-3_x`.
- [ ] Mergear `dev-php-7.4` en `stable-php-7.4` del core y fijar las tres ramas del plugin al mismo tag.
- [ ] Los tres wrappers apuntan al commit `11aec04` del core, que está en `dev-php-7.4`, pero `dev-php-7.4` ya avanzó con documentación (`8c8802c`, PR #23). Actualizar el puntero del submódulo.
- [ ] Limpiar las ramas `feat/*` y `fix/*` ya mergeadas, en el plugin y en el core.
- [ ] El plan nombraba las ramas `ojs-stable-3.3` y el core en `main`; en la práctica son `stable-3_x` y `dev-3_x`, y en el core `stable-php-7.4` y `dev-php-7.4`. Actualizar el plan o la documentación técnica (E6-T5).
- [ ] `dark-pubid-plugin/` (clon de `main`) tiene solo el commit inicial y no refleja el código. Decidir si `main` queda como README o se elimina.

## dARKCore
- [ ] **No hay cliente HTTP de la API de dARK.** Se planeó con cURL en el core, sin depender del cliente de cada OJS. Es el centro de la Entrega 3.
- [ ] `ApiResult` está definido (none, transient, permanent), pero nadie lo usa todavía.
- [ ] `Article` (estados `queued`, `published`, `declined`) está definido, pero la importación usa `ArticleOaiArk`. Revisar si `Article` sigue sirviendo para E3 o se rehace.
- [ ] Faltan DTOs: `EditorialEvent` (rechazo, despublicación, retractación, versión), `OperationRecord` (auditoría) y los de la solicitud de reserva y registro.
- [ ] `ArkRecord`: faltan los estados de fin de ciclo (tombstone, reservado vencido o liberado, retractado, versionado) y un mapeo explícito con los estados del minter (R, D, P, U y T).
- [ ] **Inconsistencia en la documentación:** el docblock de `ArkRecord::$ark` dice que la forma canónica es `ark:/NAAN/nombre`, pero `normalizeArk()` devuelve `ark:NAAN/nombre`, la forma compacta que coincide con `dark-core-lib`. Corregir el comentario.
- [ ] `JournalConfig::$apiKey` está documentado como «Bearer token». Cambiarlo cuando se defina la autenticación (posible Ed25519: clave privada y key id).
- [ ] `JournalConfigValidator::isValidResolverUrl()` rechaza las URL con path. Si el resolver definitivo vive en `/api/v1/arks/` u otra ruta, hay que relajarlo.
- [ ] Validación del dígito de control NOID en el core, si se mantiene la carga manual.
- [ ] Detección de cambios de L1 y L2 por checksum (E4-T2).
- [ ] La «tarea periódica» como funcionalidad del core «de a un paso y retomable».

## Wrappers (3.3, 3.4 y 3.5)
- [ ] **Requisito de PHP en 3.3.** El core exige 7.4 y OJS 3.3 admite 7.3. Ver [[Pendientes y dudas abiertas#10. Decisiones internas pendientes (equipo)]].
- [ ] Reponer la opción de ocultar la exposición pública (`isPublicExposed`, quitada el 11/10) si LA Referencia pide ocultar los ARK durante el piloto.
- [ ] `citation_dark` sale solo por el plugin de Google Scholar; quitarla si así se decide.
- [ ] Persistencia de las operaciones pendientes y del registro de operaciones: tablas propias con migraciones Laravel (`getInstallMigration()`).
- [ ] Tareas programadas: `scheduledTasks.xml` en 3.3 y 3.4, Laravel Scheduler en 3.5, y jobs en 3.4 y 3.5.
- [ ] Pantalla de estado de las asignaciones (E3-T5) y de auditoría (E5-T6). En 3.5 es Vue 3, la más costosa.
- [ ] Asignación individual y masiva para artículos publicados sin ARK (E3-T4).
- [ ] Opción «sobrescribir el ARK existente» en la importación CSV, si se pide.
- [ ] La importación lee el CSV entero en memoria (límite de 5 MB y 2000 filas). Alcanza para el piloto; revisar si llegan archivos más grandes.

## Sandbox
- [ ] **Mock del minter** (previsto en `INFRAESTRUCTURA.md` §8 para la Etapa 3). Todavía no existe.
- [ ] La documentación dice PHP 7.3 para OJS 3.3, pero el `Dockerfile` usa `php:7.4-apache`. Corregir la documentación.
- [ ] Hay un `images/ojs33/.Dockerfile.swp` (swap de vim) en el repo. Borrarlo o agregarlo a `.gitignore`.
- [ ] Faltan datos de prueba para la Entrega 3: una revista con publicación continua y otra por números, con artículos en distintos estados (aceptado, en producción, publicado, despublicado).

## Documentación (E6)
- [ ] Guía de instalación y configuración por versión de OJS.
- [ ] Manual para editores (asignación, importación, estados, auditoría).
- [ ] Documentación técnica del core: catálogo de funcionalidades, DTOs y política de versiones.
- [ ] Completar `README.md` del plugin y del core; hoy son una línea.
