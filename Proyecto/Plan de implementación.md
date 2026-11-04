# Plan de implementación

Resumen del documento «Plan de implementación – Plugin dARK para OJS» (rev. 2, 01/10/2026), con el **estado de cada tarea** al 04/11. Referencias: [[Propuesta de plugin]] · [[Estado del proyecto]] · [[Pendientes y dudas abiertas]].

**Estados:** Hecha · Parcial · Bloqueada · Pendiente · Verificar

**Capacidad planificada:** 10 h semanales por persona (8 h en las semanas con feriado), unas 104 h por persona. Carga: Lautaro 85 h (82 %), Santiago 84 h (81 %).

## Cronograma

| Sem | Fechas | Entrega | Hito |
|---|---|---|---|
| 1 | 28/09 – 02/10 | 0 Especificación | Especificación aprobada |
| 2–3 | 05/10 – 16/10 | 1.1 Núcleo y plugin base | Entrega 1.1 |
| 4–5 | 19/10 – 30/10 | 1.2 Importación CSV | Fin de la Etapa 1 |
| 6–7 | 02/11 – 13/11 | 2.1 Asignación de ARK | Entrega 2.1 |
| 8 | 16/11 – 20/11 | 2.2 Actualización de metadatos | Entrega 2.2 |
| 9–10 | 23/11 – 04/12 | 2.3 Ciclo de vida | Fin de la Etapa 2 |
| 11 | 07/12 – 11/12 | Piloto y documentación | Versión 1.beta |
| — | 14/12 – 16/12 | Contingencia | Cierre |

Feriados en las semanas 3, 8 y 11.

## Entrega 0: Especificación de integración
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E0-T1 | Relevamiento técnico de OJS 3.3, 3.4 y 3.5 (hooks y eventos, OAI) | Santiago | 5 | Verificar. No hay un documento con la tabla de hooks en el repo; lo usado está en [[Wrappers OJS 3.3, 3.4 y 3.5]] |
| E0-T2 | Sandbox ojs-sandbox | Santiago | 2 | Hecha |
| E0-T3 | Especificación de la API de dARK | Lautaro | 5 | Parcial. Borrador. Ver [[API del minter]] |
| E0-T4 | Formato del CSV y reglas de duplicados | Lautaro | 2 | Parcial. Implementado; falta confirmar el formato con LA Referencia |
| E0-T5 | Repositorios del plugin y del core | Lautaro | 2 | Hecha Falta documentar la política de versiones |

## Entrega 1: Núcleo y plugin base
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E1-T1 | Catálogo de funcionalidades del core | Ambos | 2+2 | Hecha |
| E1-T2 | DTOs (JournalConfig, Article, ArkRecord, ApiResult) | Ambos | 3+2 | Hecha |
| E1-T3 | Configuración por revista | Lautaro | 5 | Hecha |
| E1-T4 | Almacenamiento del ARK en el artículo | Santiago | 6 | Hecha |
| E1-T5 | Enlace en la página del artículo | Lautaro | 2 | Hecha |
| E1-T6 | Metadatos HTML y OAI-PMH | Santiago | 4 | Hecha Falta definir `DC.Identifier.dARK` o `.ark` |
| E1-T7 | Pruebas de integración y liberación | Lautaro | 4 | Hecha Tags `e1-*` (28/10) |

## Entrega 2: Importación por CSV
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E2-T1 | Catálogo del core | Lautaro | 3 | Hecha |
| E2-T2 | DTOs (CsvDocument, CsvRecord, ImportAnalysis, ImportSummary, ParserError) | Lautaro | 3 | Hecha |
| E2-T3 | Importación en OJS | Santiago | 12 | Hecha En las tres ramas |
| E2-T4 | Plan de aplicación y reporte exportable | Lautaro | 7 | Hecha |
| E2-T5 | Pruebas con CSV real y liberación | Santiago | 3 | Bloqueada. Falta el CSV real; tag `e2` pendiente |

## Entrega 3: Asignación de ARK
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E3-T1 | Catálogo del core | Santiago | 2 | Pendiente |
| E3-T2 | DTOs (solicitud de reserva o registro, resultado de la API, estado) | Santiago | 3 | Pendiente |
| E3-T3 | Reserva de ARK (pre o post publicación, manual o automática) | Lautaro | 8 | Bloqueada. Autenticación y sandbox |
| E3-T4 | Asignación individual y masiva para publicados sin ARK | Santiago | 7 | Pendiente |
| E3-T5 | Gestor de estado y sincronización periódica | Santiago | 6 | Pendiente |
| E3-T6 | Errores y reintentos | Lautaro | 5 | Bloqueada. Política sin confirmar |
| E3-T7 | Pruebas contra el sandbox y liberación | Lautaro | 4 | Bloqueada |

## Entrega 4: Actualización de L1 y L2
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E4-T1 | Catálogo del core | Santiago | 1 | Pendiente |
| E4-T2 | Detección de cambios (checksum) | Santiago | 3 | Pendiente |
| E4-T3 | Envío de actualizaciones | Lautaro | 3 | Pendiente |
| E4-T4 | Gestor de estado de las actualizaciones | Santiago | 3 | Pendiente |
| E4-T5 | Errores y reintentos | Lautaro | 2 | Pendiente |
| E4-T6 | Pruebas y liberación | Lautaro | 2 | Pendiente |

## Entrega 5: Ciclo de vida
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E5-T1 | Catálogo del core | Lautaro | 2 | Pendiente |
| E5-T2 | DTOs y estados de fin de ciclo (EditorialEvent, OperationRecord) | Lautaro | 3 | Pendiente |
| E5-T3 | ARK reservados sin publicación (plazo configurable) | Lautaro | 5 | Bloqueada. Sin definición |
| E5-T4 | Despublicación y retractación | Santiago | 5 | Bloqueada. Sin definición |
| E5-T5 | Nuevas versiones | Santiago | 6 | Bloqueada. Sin definición |
| E5-T6 | Registro de operaciones y auditoría (filtros y exportación CSV) | Santiago | 6 | Pendiente |
| E5-T7 | Regresión completa y liberación | Lautaro | 6 | Pendiente |

## Entrega 6: Piloto y documentación
| ID | Tarea | Resp | h | Estado |
|---|---|---|---|---|
| E6-T1 | Empaquetado de la versión candidata | Lautaro | 2 | Pendiente |
| E6-T2 | Instalación en las revistas piloto | Santiago | 3 | Pendiente |
| E6-T3 | Corrección de incidencias | Lautaro | 3 | Pendiente |
| E6-T4 | Documentación de instalación y uso | Santiago | 2 | Pendiente |
| E6-T5 | Documentación técnica | Lautaro | 2 | Pendiente |
| E6-T6 | Informe del piloto y liberación de la 1.beta | Santiago | 1 | Pendiente |

## Ruta crítica (50 h)
E0-T1 → E1-T1 → E1-T2 → E3-T1 → E3-T2 → **E3-T3** → **E3-T5** → **E5-T6** → **E5-T7** → E6-T1 → E6-T2 → E6-T3 → E6-T6

Depende de factores externos: la especificación de la API (E0-T3), el sandbox de minteo (E3-T3), el de actualización (E4-T3) y las definiciones de ciclo de vida (E5-T2 a E5-T5).

## Riesgos principales
| Riesgo | Prob | Impacto | Mitigación |
|---|---|---|---|
| Demora de las APIs de LA Referencia | Media | Alto | Mock según la especificación; cliente aislado en el core |
| Ciclo de vida sin definir | Alta | Alto | Priorizar los ARK reservados; políticas configurables y experimentales |
| Diferencias entre versiones de OJS (sobre todo 3.5) | Media | Alto | Santiago lleva 3.3 y 3.5 |
| Red dARK en estado piloto (persistencia) | Media | Alto | Garantías antes de exponer; opción de ocultar |
| El OAI predicho no coincide con el real | Media | Alto | Confirmar después de publicar; actualizar L1 |
| Core incompatible con PHP 7.3 – 8.2 | Media | Medio | CI multi-PHP. **Hoy el core exige 7.4** |
| Cron, Acron, Scheduler o colas sin configurar en el piloto | Alta | Medio | Documentar; acciones manuales |
| Feriados y menor dedicación | Alta | Medio | Plan al 80 %; contingencia |
| Faltan revistas para el piloto | Media | Alto | Confirmarlas antes de la semana 8 |

> [!note] Supuesto sobre L1 y L2
> El plan suponía que L1 era «ARK → URL y OAI» y L2 «metadatos descriptivos». El minter define otra cosa: L1 = `minimal_metadata` y L2 = `original_metadata`. Ver [[API del minter#Metadatos L1 y L2]].
