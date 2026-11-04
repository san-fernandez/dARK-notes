# Estado del proyecto

Foto al **04/11/2026**: semana 6 de 11. Ver también [[Plan de implementación]] y [[Pendientes y dudas abiertas]].

## Por entrega

| Entrega | Qué incluye | Semanas | Estado |
|---|---|---|---|
| 0. Especificación | Credenciales, endpoints, CSV, OAI identifier | 1 | **Borrador.** Le faltan las respuestas de LA Referencia; no está aprobada |
| 1. Núcleo y plugin base | Ajustes, ARK en la publicación, página, HTML, OAI-PMH | 2–3 | Hecha en las tres ramas. Tags `e1-ojs3.3`, `e1-ojs3.4`, `e1-ojs3.5` y `e1` en el core (28/10) |
| 2. Importación por CSV | Validación previa, reporte, aplicación | 4–5 | Hecha en `dev-3_3`, `dev-3_4` y `dev-3_5` (PR #34, 30/10, más arreglos hasta el 02/11). **Sin tag ni merge a `stable-*`** |
| 3. Asignación de ARK | Reserva y registro contra el minter | 6–7 | **En curso, bloqueada.** No hay sandbox del minter, credenciales ni definición de autenticación |
| 4. Actualización L1/L2 | Envío de cambios de metadatos | 8 | Pendiente. Necesita la API de actualización |
| 5. Ciclo de vida | Reservas sin publicar, retractación, versiones, auditoría | 9–10 | Pendiente. Necesita definiciones de LA Referencia |
| 6. Piloto y documentación | Revistas reales, versión 1.beta | 11 | Pendiente. Necesita revistas piloto |

## Qué funciona hoy
- **Instalación:** un único paquete registra dos plugins, el de identificador público (pubIds, «dARK») y el de importación (CSV).
- **Ajustes por revista:** UUID, prefijo ARK, API key de solo escritura, URL del resolver y momento de asignación. El core los valida. Ver [[Configuración, asignación y exposición]].
- **Asignación manual** desde Publicación → Identificadores. Se valida el formato, el prefijo y la unicidad, y aparece un aviso en el paso de publicación.
- **Exposición:** enlace en la página del artículo, `DC.Identifier.dARK` (y `citation_dark`) en el HTML y `dc:identifier` en OAI-PMH.
- **Importación CSV** con reporte previo, aplicación de las filas válidas y reporte descargable. Ver [[Importación por CSV]].
- **dARKCore:** 118 tests con PHPUnit y CI en PHP 7.4, 8.0, 8.2 y 8.3. Ver [[dARKCore]].

## Qué no existe todavía
- Cliente HTTP de la API de dARK (reserva, registro, consulta, tombstone).
- Servidor simulado (mock) del minter. El sandbox lo preveía para la Etapa 3.
- Estados `reserved` y `registered` en uso real: hoy solo se usan `unassigned` y `manual_assigned`.
- Tarea periódica de sincronización, jobs y lotes.
- Detección de cambios de metadatos (Entrega 4).
- Registro de operaciones o auditoría (`OperationRecord`) y DTO `EditorialEvent`.

## Bloqueos principales
1. **Autenticación sin definir:** API key, mTLS o firma Ed25519. Ver [[API del minter#Autenticación]].
2. **Sin sandbox del minter ni credenciales de prueba.** El plan los esperaba antes de la semana 6.
3. **Sin acceso a `dark-deployer`.** El repo dejó de estar visible; nuestra copia es del 24/09.
4. **Definiciones de ciclo de vida** (Entrega 5): tombstone, despublicación, versiones.

> [!warning] Riesgo de calendario
> Si el sandbox no llega esta semana, la Entrega 3 se corre sobre la 4. La mitigación prevista es desarrollar contra un mock según lo que relevamos de `dark-deployer` y `dark-core-lib`.

## Próximos pasos internos
- [ ] Volcar las respuestas de la reunión del 04/11 en [[2026-11-04 Reunión con LA Referencia]] y actualizar [[Pendientes y dudas abiertas]].
- [ ] Etiquetar la Entrega 2 (`e2-ojs3.x` y el tag del core) y mergear `dev-*` en `stable-*`.
- [ ] Armar el mock del minter (FastAPI o PHP) con los endpoints de [[API del minter]].
- [ ] Catálogo de funcionalidades y DTOs de la Entrega 3 (E3-T1 y E3-T2).
- [ ] Prototipo de firma Ed25519 con `sodium` en PHP, por si se confirma ese mecanismo.
