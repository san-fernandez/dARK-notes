## 1. Objetivo y alcance

Se pretende implementar un prototipo para OJS que permita asignar identificadores dARK (_Decentralized Archival Resource Key_) a artículos publicados o por publicar. Este prototipo será puesto a prueba en al menos dos instituciones para validar su funcionamiento.

A diferencia del plugin de ARK para OJS (disponible en [https://github.com/yasielpv/pkp-ark-pubid](https://github.com/yasielpv/pkp-ark-pubid)), este plugin permitirá no solo registrar los PID en OJS, sino también reservar y registrar nuevos PID en la infraestructura de dARK y actualizar los existentes que puedan estar asociados a cada revista. Este plugin no requerirá el uso de un _resolver_ (como PubIdResolver), ya que la resolución se realizará a través de la infraestructura de dARK.

El proyecto desarrollará, en **11 semanas de trabajo** (del 28/09/2026 al 11/12/2026, con contingencia hasta el 16/12/2026), un plugin para OJS 3.3, 3.4 y 3.5 que incorpora identificadores ARK asignados mediante dARK a los artículos de las revistas.

Las claves de diseño para el nuevo plugin serán:

- **Objeto a identificar:** se asignarán identificadores a artículos aceptados o publicados. No se asignarán ARK a envíos no aceptados, números ni galeradas.
    
- **Momento de asignación:** configurable por revista; podrá realizarse antes de la publicación (para incluir el ARK en las galeradas) o después de ella.
    
- **Clave de vinculación:** según el caso, podrá utilizarse:
    
    - el identificador OAI (_OAI Identifier_) del artículo, cosechado o predicho a partir de la configuración de OJS; o
        
    - la URL pública del artículo.
        
- **Exposición del ARK:** el identificador asignado será visible en:
    
    - la página del artículo (mediante un enlace);
        
    - los metadatos HTML (`DC.Identifier.ark`);
        
    - el proveedor OAI-PMH de OJS (en `dc:identifier` o campo equivalente).
        
- **Comunicación:** siempre será iniciada desde el plugin hacia el nodo central de dARK.
    

---

## 2. Arquitectura

El plugin se compone de un núcleo común y tres adaptadores, uno por versión de OJS.

### Núcleo común

Responsable de:

- Lógica de negocio.
    
- Cliente de la API central.
    
- Validaciones.
    
- Cálculo del identificador OAI.
    
- Registro de operaciones.
    

### Adaptadores (OJS 3.3, 3.4 y 3.5)

Responsables de:

- Integración con la interfaz.
    
- Acceso a datos.
    
- Hooks específicos de cada versión.
    
- Ruteo y compatibilidad con cada versión de OJS.
    

### Autenticación

La autenticación se realizará mediante:

- UUID de revista.
    
- Prefijo ARK.
    
- API Key.
    

Los tres valores serán emitidos por LA Referencia y serán válidos para todas las versiones de OJS.

El plugin cumplirá con las recomendaciones de desarrollo establecidas por la comunidad PKP.

---

## 3. Etapas y entregables

El proyecto se organiza en una etapa de acuerdos técnicos y dos etapas de desarrollo, con siete entregas en total.

Cada entrega se liberará para OJS 3.3, 3.4 y 3.5.

### Entrega 0 – Especificación de integración

Documento acordado con LA Referencia que define:

- Formato del CSV de importación.
    
- Endpoints de la API central.
    
- Esquema de autenticación (UUID de revista, prefijo ARK y API Key).
    
- Regla de construcción del identificador OAI futuro y su validación por parte de LA Referencia.
    
- Reglas de validación de duplicados.
    
- Formato de las actualizaciones de metadatos, incluido el cambio de host.
    

### Entrega 1 – Núcleo y plugin base

- Núcleo común y adaptadores para las tres versiones de OJS.
    
- Configuración por revista:
    
    - UUID.
        
    - Prefijo ARK.
        
    - API Key.
        
    - URL del resolver.
        
- Almacenamiento del ARK asociado a cada artículo.
    
- Visualización del ARK como enlace en la página del artículo.
    
- Exposición en metadatos HTML y en OAI-PMH.
    

### Entrega 2 – Importación por CSV

- Carga de pares `(OAI Identifier, ARK)`.
    
- Validación previa sin aplicar cambios.
    
- Reporte de:
    
    - artículos no encontrados;
        
    - conflictos;
        
    - duplicados.
        
- Aplicación de la importación y registro de resultados.
    

### Entrega 3 – Asignación de ARK

- Reserva de ARK al nodo _minter_ (sandbox), antes o después de la publicación según la configuración de la revista.
    
- Ejecución automática o manual.
    
- Soporte para distintos ciclos editoriales:
    
    - publicación continua;
        
    - publicación por número.
        
- En publicaciones por número, posibilidad de reserva o registro en lote para todos los artículos del número.
    
- Registro posterior de:
    
    - URL de acceso;
        
    - identificador OAI (si corresponde).
        
- Asignación individual y masiva para artículos publicados sin ARK.
    
- Tarea periódica de sincronización para relevar el estado de las asignaciones y actualizaciones pendientes.
    
- Gestión de errores y reintentos.
    

### Entrega 4 – Actualización de registros L1 y L2

- Envío automático de actualizaciones cuando cambian los metadatos de un artículo con ARK.
    

### Entrega 5 – Cierre del ciclo de vida

- Gestión de ARK reservados cuyos artículos no llegan a publicarse.
    
- Tratamiento de artículos despublicados o retractados.
    
- Tratamiento de nuevas versiones de un artículo.
    
- Registro de operaciones para auditoría.
    

### Entrega 6 – Piloto y documentación

- Prueba piloto con revistas reales en las tres versiones de OJS.
    
- Corrección de incidencias detectadas.
    
- Documentación de instalación, configuración y uso.
    
- Liberación del prototipo (**versión 1.beta**).
    

---

## 4. Extensiones a futuro

A continuación se expresan algunas funcionalidades adicionales para discutir en futuros desarrollos:

- Permitir el registro retrospectivo de ARK para todos los artículos de un número ya publicado.
    
- Permitir el registro de ARK para números (_issues_).
    
- Permitir la asignación de dARK para artículos prerregistrados mediante ISSN.
    
- Integración con marcado XML-JATS, prevista para una fase posterior.
    

---

## 5. Estimación de tiempos

El proyecto requiere **11 semanas calendario** y **22 persona-semanas de esfuerzo**, con ambos desarrolladores trabajando en paralelo sobre cada entrega.

|Entrega|Duración (semanas)|Requiere de LA Referencia|
|---|---|---|
|0. Especificación de integración|1|Participación en la definición y validación|
|1. Núcleo y plugin base|2|Credenciales de prueba (UUID, prefijo y API Key)|
|2. Importación por CSV|2|CSV de ejemplo con ARK reales|
|3. Asignación de ARK|2|API de _minting_ disponible en entorno de pruebas|
|4. Actualización de metadatos|1|API de actualización disponible en entorno de pruebas|
|5. Cierre del ciclo de vida|2|Definición de estados de ARK reservados, retirados y versionados|
|6. Piloto y documentación|1|Participación en la validación del piloto|

La estimación incluye pruebas en las tres versiones de OJS dentro de cada entrega.

Se reservan **tres días hábiles de contingencia** (14/12 al 16/12/2026).

---

## 6. Cronograma

La **Etapa 1** finaliza el **30/10/2026**, la **Etapa 2** el **04/12/2026** y el proyecto el **11/12/2026**.

|Semana|Fechas|Entrega|Hito|
|---|---|---|---|
|1|28/09 – 02/10|0.1 Especificación de integración|Especificación aprobada|
|2|05/10 – 09/10|1.1 Núcleo y plugin base||
|3|12/10 – 16/10|1.1 Núcleo y plugin base|Entrega 1.1|
|4|19/10 – 23/10|1.2 Importación por CSV||
|5|26/10 – 30/10|1.2 Importación por CSV|Entrega 1.2 – Fin de Etapa 1|
|6|02/11 – 06/11|2.1 Asignación de ARK||
|7|09/11 – 13/11|2.1 Asignación de ARK|Entrega 2.1|
|8|16/11 – 20/11|2.2 Actualización de metadatos|Entrega 2.2|
|9|23/11 – 27/11|2.3 Cierre del ciclo de vida||
|10|30/11 – 04/12|2.3 Cierre del ciclo de vida|Entrega 2.3 – Fin de Etapa 2|
|11|07/12 – 11/12|Piloto y documentación|Versión 1.beta|
|—|14/12 – 16/12|Contingencia|Cierre del proyecto|

Las semanas 3, 8 y 11 incluyen feriados nacionales que reducen la capacidad disponible y quedan cubiertos por la contingencia.

---

## 7. Equipo de trabajo

El equipo se constituirá por dos profesionales informáticos con amplia experiencia comprobable en desarrollo de aplicaciones PHP y OJS dentro de SEDICI (UNLP).

|Rol|Perfil|Foco principal|
|---|---|---|
|Santiago Fernandez|PHP y aplicaciones web; amplia experiencia en OJS|Adaptadores, integración con la interfaz y el ciclo editorial de OJS|
|Lautaro Josín Saler|PHP y aplicaciones web|Núcleo común, cliente de la API, importación y sincronización|

Ambos desarrolladores tendrán dedicación completa al proyecto.

---

## 8. Presupuesto

El presupuesto estimado para implementar el desarrollo propuesto será de:

- **USD 1.400 por desarrollador y por mes**
    
- **2 pagos por desarrollador**
    
- **USD 5.600 en total**
    

La forma de pago será a mes vencido, en dólares estadounidenses, contra factura y mediante transferencia bancaria a cada miembro del equipo de desarrollo.

En caso de que la transferencia genere gastos bancarios o impositivos en el país de origen, dichos costos correrán por cuenta del contratante.