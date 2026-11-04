# Plugin dARK para OJS

Proyecto de SEDICI (UNLP) para LA Referencia: un plugin para OJS 3.3, 3.4 y 3.5 que asigna identificadores **dARK** (ARK descentralizados) a los artículos, los registra en la red dARK y los expone en la página del artículo, en los metadatos HTML y en OAI-PMH.

- **Período:** 28/09/2026 – 11/12/2026, con contingencia hasta el 16/12/2026.
- **Equipo:** Santiago Fernández (wrappers 3.3 y 3.5, integración con OJS) y Lautaro Josín Saler (dARKCore, wrapper 3.4, cliente de la API).
- **Contraparte:** Lautaro Matas (LA Referencia).

> [!info] Dónde estamos (04/11/2026, semana 6 de 11)
> Las Entregas 1 y 2 ya están integradas en las tres ramas. La Entrega 3, asignación contra el minter, depende del sandbox y de las credenciales de LA Referencia. Ver [[Estado del proyecto]].

## Proyecto
- [[Estado del proyecto]]: qué está hecho, qué sigue y qué bloquea.
- [[Plan de implementación]]: entregas, tareas, responsables y estado.
- [[Propuesta de plugin]]: la propuesta original (objetivo, alcance, cronograma y presupuesto).
- [[Pedidos a LA Referencia]]: lo que necesitamos de ellos y para cuándo.

## Pendientes
- [[Pendientes y dudas abiertas]]: todas las definiciones que faltan, agrupadas por tema.
- [[Deuda técnica]]: lo pendiente del lado del código.
- [[Fase 2 y extensiones]]: lo que queda fuera del alcance de 2026.

## dARK
- [[Qué es dARK]]: la plataforma, sus capas y los estados de un ARK.
- [[API del minter]]: endpoints, autenticación, metadatos L1 y L2, errores y reintentos.
- [[Notas Lauti]]: diferencias entre ARK y dARK y tests del core.

## Desarrollo
- [[Arquitectura del plugin]]: el core, los wrappers, los repositorios y las ramas.
- [[dARKCore]]: clases, DTOs, tests y CI.
- [[Wrappers OJS 3.3, 3.4 y 3.5]]: diferencias entre versiones y hooks que usamos.
- [[Configuración, asignación y exposición]]: Entrega 1.
- [[Importación por CSV]]: Entrega 2.
- [[Propuesta Commits]]: convención de commits, ramas y PRs.
- [[¿Cómo usar?|Sandbox]]: entorno Docker con las tres versiones de OJS.

## Bitácora y reuniones
- [[Notas Santi]] · [[Notas Lauti]]
- [[2026-11-04 Reunión con LA Referencia]]

