# Fase 2 y extensiones a futuro

Lo que **queda fuera del alcance de 2026** según el plan de trabajo (sección 4 y Anexo E). Para lo pendiente dentro del alcance, ver [[Pendientes y dudas abiertas]] y [[Deuda técnica]].

## Extensiones a discutir (sección 4 del plan)
- Registro retrospectivo de ARK para todos los artículos de un número ya publicado.
- ARK para números (*issues*).
- Asignación de dARK para artículos prerregistrados, pidiendo por ISSN.
- Integración con el marcado XML-JATS.

## Posibles incorporaciones para una Fase 2 (2027)

| Funcionalidad | Descripción | Condición previa |
|---|---|---|
| Sincronización por API REST | Consultar al nodo central los ARK de la revista e incorporarlos a OJS, a mano o con una tarea programada | API de consulta de LA Referencia en producción |
| Actualización masiva por cambio de host | Enviar en lote las nuevas URL y OAI identifiers de una revista que migra de servidor | API de actualización con soporte para lotes |
| Cambio de `repository_id` | Detectar revistas que cambian el `repository_id` y actualizar los OAI identifiers de sus ARK | Regla acordada con LA Referencia para reemplazar OAI identifiers |
| Integración con XML-JATS | Incluir el ARK en el JATS y en las galeradas generadas desde él | Reserva previa a la producción (Fase 1) |
| Cómo citar | ARK en su forma canónica dentro del bloque «Cómo citar» | Forma canónica y URL de resolución definidas |
| Qualifiers por formato | Variante del ARK por galerada (PDF, HTML) | Soporte de qualifiers en dARK y en el resolver |
| Qualifiers por versión | ARK que identifica versiones del artículo | Decidir si una versión conserva el ARK o recibe uno nuevo |

## Ideas surgidas durante el desarrollo
- ARK en otros formatos OAI (`oai_openaire`, JATS), si LA Referencia lo pide. Ver [[Pendientes y dudas abiertas#4. Exposición del ARK]].
- Validar en lote contra el minter que los ARK de un CSV existan y pertenezcan a la revista (`POST /arks/status/batch`).
- Aceptar el formato de CSV de la herramienta del minter (`darkidentifier`, `itemurl`).
- Guardar el secreto de autenticación cifrado o en una variable de entorno.
