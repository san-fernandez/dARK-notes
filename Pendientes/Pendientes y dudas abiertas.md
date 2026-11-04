# Pendientes y dudas abiertas

Todo lo que falta **definir** (con LA Referencia o entre nosotros) para seguir desarrollando. Sale de:
- la presentación y las dudas de la reunión del 04/11 ([[2026-11-04 Reunión con LA Referencia]]);
- las secciones «a confirmar» de la especificación de la Etapa 0;
- el Anexo C (dudas) y el Anexo D (riesgos) del plan de trabajo.

Lo pendiente de **código** está en [[Deuda técnica]], y lo que queda fuera de alcance en [[Fase 2 y extensiones]].

> [!tip] Cómo usar esta nota
> Cuando una duda se responda, tildala y escribí la respuesta debajo con `→ Respuesta (fecha, quién):`. Si cambia el diseño, pasala también a la nota técnica correspondiente.
> **Bloquea** indica qué entrega frena.

---

## 1. Autenticación y credenciales
**Bloquea:** E1 (ajustes) y E3 (todo el cliente de la API).

Contexto: desde el 13/10, `dark-core-lib` firma cada request con **Ed25519**. Nuestra copia del minter (24/09) todavía autentica por **mTLS** o por el header `X-Authority-Id`. En ningún lado del código aparece una API key, aunque el plugin hoy pide una. Ver [[API del minter#Autenticación]].

- [ ] ¿OJS va a usar la firma Ed25519, en lugar de API key o mTLS?
- [ ] ¿Quién genera el par de claves, la revista o LA Referencia?
- [ ] ¿Cómo se registra la clave pública, cómo se cambia y cómo se revoca? (`set_authority_client_key` lo puede hacer solo el administrador).
- [ ] ¿Qué tolerancia de reloj se usa para el timestamp? El diseño propone 300 s. Los servidores de OJS van a necesitar NTP.
- [ ] ¿Tienen un request firmado de ejemplo o vectores de prueba para validar nuestra implementación con `sodium`?
- [ ] ¿El minter actual ya verifica firmas? ¿El sandbox las va a exigir también para las lecturas (GET)?
- [ ] Nombre definitivo del header de autoridad: `X-Authority-Id`, `X-Authority-UUID` o `X-DARK-Authority-Id` (el de la firma).
- [ ] ¿Cómo se almacena y protege el secreto en OJS? Hoy va en `plugin_settings` en claro y oculto en la interfaz. Opciones: cifrarlo o usar una variable de entorno `DARK_API_KEY_<JOURNAL_ID>`.
- [ ] **Interno:** si se confirma Ed25519, reemplazar el campo «API key» de los ajustes por la clave de firma, o por la clave privada y el key id.

## 2. Prefijo, shoulder y resolver
**Bloquea:** E1 (ajustes y enlace público) y E3.

- [ ] ¿Existen shoulders por revista? La reserva recibe solo `authority_id` y `naan` (`ReserveARKRequest`, con `extra=forbid`). Si existen, ¿cómo se piden?
- [ ] ¿Cuál es la URL pública definitiva del resolver? El resolver resuelve en `/api/v1/arks/{ark}`, pero el plugin solo acepta un dominio y arma `https://dominio/ark:12345/x`. ¿Va a resolver en la raíz?
- [ ] ¿Qué resolver se muestra por defecto, el global (n2t) o el de LA Referencia? (Anexo C).
- [ ] Forma canónica: desde el 31/10, `dark-core-lib` toma `ark:12345/x` como canónica y acepta las dos formas. El plugin guarda esa forma. ¿El minter y el resolver ya la usan?
- [ ] ¿Usamos `GET /api/v1/authority/{uuid}/authorized/{naan}` para validar la configuración al guardar los ajustes? Hoy requiere mTLS.

## 3. Asignación manual en la publicación
**Bloquea:** decisiones de E1 que repercuten en E3.

- [ ] El minter valida el **dígito de control NOID** (`MINTER_NOID_CHECKDIGIT`, activo por defecto). Un ARK cargado a mano que no generó el minter sería rechazado al actualizarlo o al hacerle tombstone. ¿Mantenemos la carga manual? ¿Validamos el dígito de control en OJS?
- [ ] Publicar sin ARK hoy solo muestra un aviso. ¿Debería bloquear la publicación? Nuestra postura: no.
- [ ] Asignación previa a la publicación: ¿reservamos cuando se acepta el artículo o cuando pasa a producción?
- [ ] Si el editor cambia o borra un ARK que ya está registrado en dARK, ¿qué pasa con el anterior? ¿Tombstone, o se bloquea la edición?

## 4. Exposición del ARK
**Bloquea:** E1, cosmético pero visible.

- [ ] Nombre de la metaetiqueta: OJS genera `DC.Identifier.dARK` (sale del tipo de pubId) y el plan decía `DC.Identifier.ark`. ¿Cuál espera LA Referencia?
- [ ] OJS agrega también `citation_dark` vía Google Scholar, que no es estándar. ¿La dejamos o la quitamos?
- [ ] En OAI-PMH sale `dc:identifier` = `ark:12345/art2`, sin URL. ¿Lo quieren así o como URL de resolución?
- [ ] ¿Hace falta el ARK en otros formatos OAI (`oai_openaire`, JATS)?
- [ ] Durante el piloto, ¿mostramos el ARK en público o lo ocultamos hasta tener garantías de persistencia? Riesgo «estado piloto de la red». Ojo: se quitó la opción `isPublicExposed` (refactor del 11/10); si se decide ocultar, hay que reponerla.

## 5. Importación por CSV
**Bloquea:** cierre de E2 (pruebas con datos reales, E2-T5).

- [ ] La herramienta de importación del minter (`app/cli/import_arks.py`) usa las columnas `darkidentifier` e `itemurl` y vincula por la URL del ítem, no por el OAI. ¿El CSV que nos van a pasar tiene ese formato? ¿Aceptamos también esas columnas?
- [ ] Columnas definitivas. El plugin usa `oai` y `ark`, las dos obligatorias. La especificación proponía `oai_identifier`, `target` y `ark`, con `ark` obligatorio y al menos uno de los otros dos.
- [ ] Codificación y separadores: hoy UTF-8 (con o sin BOM), coma o punto y coma. ¿Se confirma?
- [ ] Vinculamos por el OAI que calculamos con el `repository_id` de la instalación. ¿Los ARK existentes se generaron con ese mismo OAI?
- [ ] Si el artículo ya tiene otro ARK, hoy la fila se omite. ¿Hace falta una opción para sobrescribirlo? La especificación lo menciona.
- [ ] ¿Hay que verificar contra el minter que cada ARK exista y sea de la revista antes de aplicarlo (`POST /arks/status/batch`)?
- [ ] Ver el reporte de la herramienta web de los equipos de DSpace y Dataverse, idealmente funcionando, o acceder a su código, para alinear nuestro reporte.
- [ ] **Pedido:** un CSV real con ARK existentes.

## 6. API del minter y metadatos
**Bloquea:** E3 y E4.

- [ ] El minter **no tiene campo para el OAI identifier**, y el plan lo usa como clave de vinculación. ¿Dónde lo enviamos? ¿En `alternate_identifiers`? (La reserva no lo acepta, así que no hay que enviarlo al reservar).
- [ ] ¿Cómo valida LA Referencia un OAI todavía no cosechado? ¿Qué pasa si no coincide con el que se cosecha después? (Anexo C).
- [ ] L1: el código exige `title`, `authors` (lista), `year` (entero) y `original_metadata {schema, media_type}`, pero la documentación de la API muestra `creator` y `date`. ¿Vale lo que dice el código?
- [ ] L2: `metadata_schema` acepta `dublin_core`, `oai_dc`, `datacite`, `openaire4`, `jats` y `other`. Proponemos enviar el `oai_dc` que ya genera OJS. ¿Les sirve?
- [ ] ¿Los endpoints, headers y cuerpos relevados ([[API del minter#Endpoints]]) son los definitivos?
- [ ] `POST /arks/status/batch` no figura en la documentación de la API. ¿Es estable?
- [ ] **Pedido:** fecha del sandbox del minter y de las credenciales de prueba. El plan los esperaba antes de la semana 6.
- [ ] **Pedido:** acceso de lectura a `dark-deployer` (dejó de estar visible) o la versión vigente de la API.

## 7. Errores, reintentos y límites
**Bloquea:** E3-T6 y E4-T5.

- [ ] Hay dos formatos de error. Los de la librería devuelven `{error, message, retryable, details}` con los headers `X-DARK-Error-Code` y `X-DARK-Retryable`. El resto devuelve solo `{detail}`, sin headers (409 por tombstone o por operación pendiente, 400 por dígito de control, 404). ¿Se van a unificar?
- [ ] Tabla completa de códigos. La especificación lista 401, 403, 404, 409, 422 y 500, pero el código también devuelve 400, 502 y 503. ¿Se confirma?
- [ ] El **409** significa cosas distintas. Unos se pueden reintentar (en proceso, colisión al reservar) y otros no (tombstone, creación o actualización pendiente). ¿Los distinguimos por `X-DARK-Error-Code`?
- [ ] Política propuesta: reintentar ante 500, 502, 503 y el 409 «en proceso», con backoff exponencial, y no reintentar ante 400, 401, 403, 404 ni 422. ¿De acuerdo?
- [ ] ¿Hay límites de uso o rate limits en el proxy o la infraestructura? La reserva en lote no tiene tope en el código.

## 8. Ciclo de vida
**Bloquea:** E5 entera (riesgo alto según el plan).

- [ ] `DELETE /arks/{ark}` marca el tombstone solo en la base del minter. Todavía no se propaga a la blockchain (`TODO` en `delete_ark()`), así que el ARK sigue resolviendo. ¿Está previsto propagarlo?
- [ ] **Despublicación y retractación:** ¿tombstone, o actualizar el `target` a una página de aviso? Si se vuelve a publicar, ¿se revierte?
- [ ] **ARK reservados que nunca se publican:** no hay endpoint para liberarlos. ¿Quedan en `R` o pasan a tombstone? ¿Con qué plazo?
- [ ] ¿Habrá estados finales además de R, D, U, P y T (por ejemplo, retractado)? ¿Cuándo corresponde tombstone y cuándo dejar el ARK reservado?
- [ ] **Nueva versión de un artículo:** ¿mismo ARK actualizado con PUT (estado U) o un ARK nuevo vinculado?
- [ ] **Cambio de dominio de la revista:** ¿un PUT por artículo con el nuevo `target`, o una actualización en lote?
- [ ] ¿Qué garantías de persistencia tienen los ARK durante el piloto?

> Mitigación acordada en el plan: priorizar los ARK reservados y dejar despublicación, retractación y versiones como **política configurable y experimental**.

## 9. Actualización y sincronización (Entrega 4)
**Bloquea:** E4.

- [ ] ¿La «API de actualización» es el PUT sobre un ARK publicado (pasa a U) o va a haber otra? ¿Cuándo va a estar en el sandbox?
- [ ] Mientras no esté, proponemos guardar las actualizaciones pendientes en OJS y enviarlas cuando esté. ¿De acuerdo?
- [ ] Eventos de OJS que habilitan pedir una actualización (sin envío automático): proponemos edición de metadatos, cambio de URL y despublicación. ¿Falta alguno?
- [ ] Si el OAI identifier cambia después de reservar o publicar (cambio de `repository_id` o de dominio), ¿cómo se actualiza en dARK?
- [ ] Si los metadatos L1 o L2 de dARK difieren de los de OJS, ¿cuál manda?

## 10. Decisiones internas pendientes (equipo)
- [ ] **PHP mínimo para OJS 3.3.** dARKCore exige PHP ≥ 7.4 (`composer.json`, propiedades tipadas) y OJS 3.3 admite 7.3. Opciones: documentar 7.4 como requisito del plugin o bajar la sintaxis del core. El sandbox de 3.3 ya corre en PHP 7.4, aunque su documentación dice 7.3.
- [ ] Momento exacto de la reserva automática: hook de aceptación (decisión editorial) o paso a producción. Ver la sección 3.
- [ ] Cómo disparar la sincronización periódica en cada versión: `scheduledTasks.xml` y Acron en 3.3 y 3.4, Laravel Scheduler en 3.5, y jobs en cola en 3.4 y 3.5.
- [ ] Mock del minter: ¿FastAPI, como dice `INFRAESTRUCTURA.md` del sandbox, o un mock en PHP dentro de los tests del core?
- [ ] Revistas e instituciones para el piloto: confirmar antes de la semana 8 (riesgo del plan).

---

## Respuestas recibidas
_Completar después de la reunión del 04/11._

| Fecha | Tema | Respuesta | Impacto |
|---|---|---|---|
| | | | |
