# Antecedentes

La App está en desarrollo, particularmente su _backend_. Al día presente se tiene un _MVP_ que tiene un desarrollo muy avanzado en la parte de _ciencia de datos_ y un desarrollo parcial en las _bases de datos_ (BD).

<!--La parte correspondiente al de desarrollo de ciencia de datos es está representado por los _cubos_ _Rserve_ en el esquema siguiente; la parte de la base de datos por todas las bases _DB_ y sus _REST API_.

![Arquitectura General](../../../../ownCloud/proyectos/1912_vca_app/docs/cube_microservice_rserve.svg.png)-->

<!--Las bases de datos señaladas tienen interacción sobre todo con el cubo _Rserve_, pero son alimentadas mediante algunas API desarrolladas específicamente para el caso o directamente. -->

Actualmente las BD se encuentran operando en MYSQL.

La base de datos que contiene datos transaccionales es la de _BD_Comparables_, la cual es almentada diariamente. Esta base de datos requiere un nuevo diseño e implementación, que agregue nuevas necesidades de tablas además de las ya existentes.

Hay otras bases de datos que necesitan desarrollarse o igualmente rediseñarse,
las cuales se clarificarán en el siguiente requerimiento.

Asimismo, la forma de acceso a las bases de datos es directa y se requiere de
una estructura de _RESTFUL_API_.

De igual forma, el acceso al MVP es también directo, requiriendo al
menos un _proxy_, o lo que se proponga, que gestione requests y aspectos de seguridad.

Las bases de datos se alimentan de distintas fuentes, entre ellas las que se
derivan del sistema de Sistema de Administración de Información de Inmuebles
(SAII). Este sistema está en desarrollo y en el presente requerimiento se detalla
lo necesario para su conclusión.

<!---, aunque no intensivamente.
 Los cubos _Rserve_ interaccionan diariamente con la _BD_Comparables_, principalmente, mediante la definicion de vistas, las cuales son consumidas directamente por el MVP. Dichas vistas retoman información de las tablas que la comprenden y ponen a disposición la información por _Estado_ de la república en una ventana tiempo que va de la fecha de consulta hasta 210 días anteriores. La alimentación de la DB_Comparables sucede sobre la tablas _xx_research_buildings_ donde la xx indexa el Estado de la república. Las tablas _xx_research_buildings_ contienen información del mercado inmobiliario, particularmente del mercado de _bienes inmuebles en oferta_ y momento es alimentada mediante una _API_ desarrollada en _Symfony_. El proceso de generación de la info de alimentación es independiente de la arquitectura (la información es generada por la empresa Valor Comercial Avalúos). Las vistas que generan las ventanas de tiempo de interés y adicionan algunos cambios están indicadas mediante el prefijo _v_. Se contempla integrar otras tablas con información otra información. Estas tablas serían integradas, incluyendo las de VCA, dentro de una tabla que las aglutine llamada _xx_comparables_ donde _xx_ indexa la clave según el INEDI del Estado. Este diseño de integración requiere de revisión y validación experta.
-->

<!--Las demás bases de datos suplen tanto a los cubos _Rserve_ como a la _BD_Comparables_ de información relativa a entidades no transaccionales, tales como datos del _INEGI_, de _SEPOMEX_, tablas propias, etcétera (_DB_Utils_). Funcionan como tablas maestras.-->

<!-- La BD_Settlements contiene información de colonias de los estados de la república; es una base datos _empezada_, sin terminar de desarrollar. Se contempla que tenga campos relativos a los polígonos que delimitan las colonias y que incluya una tabla de _equivalencias_ (_thesaurus_) de nombres de colonias. -->

<!-- Adicionalmente, se tiene una BD_Nocnok (no presente en la gráfica anterior y que posteriormente se llamará DB_Scrapping), y se contempla adicionarle más tablas, que son básicamente tablas recolectoras del scrapping de websites inmobiliarios. La tabla _nocnok_ ya existente en esta base de datos no tiene definida ninguna _RESTFUL API_ y su llenado es periódico y directo, de crecimiento constante con un promedio de _1000 registros diarios_. -->

<!-- También se tiene contemplado desarrollar la BD_Appraisals, producto del uso de la App. No existe ninguna DB de _usuarios_ de la App en el backend, pero se contempla su inclusión. -->

<!-- Las tablas de las BD descritas tienen todas un campo autoincremental _id_ y otros campos de índole administrativa tales como el _usuario creador_, _editor_, _timestamp de creación_ y _de modificación_. Sin embargo, estos campos _no_ son llenados automáticamente al ser actualizado/creado algún registro. -->

<!-- En el Apéndice A se describen de manera rápida los campos de cada una de las tablas de las BD.  -->

# Requerimientos de desarrollo

## General

1. Se precisa desarrollar completamente las BD del Backend de la App/API donde todas las tablas cuenten con

    - Estándares de definición/etiquetado, mejores prácticas
    - Campos administrativos autocompletables
    - Índices?
    - Llaves primarias y secundarias?
    - Escalables
    - Arquitectura de microservicios con estándares de _RESTFUL API_ para peticiones en _JSON_

2. Concluir el Sistema de Administración de Información de Inmuebles (SAII).

3. Desarrollo de un proxy (o la mejor propuesta) para el backend (acceso a la API y Webapp).

## Específicos

Nota: las longitudes en () serán definidas en el levantamiento de requerimientos.

### 1. BD_Comparables{#bd_comparables}

#### Tabla _comparables_

- Será una tabla que junte la información de otras tablas, las que por lo pronto son:

1. `research_buildings` (ya existente)
2. `SAIIA` (por construir; ver más adelante *en esta misma sección*)
3. `SAIIB` (por construir; ver más adelante *en esta misma sección*)

- Campos:

```
  `id` int(11) NOT NULL AUTO_INCREMENT
  `source_id` int(11) DEFAULT NULL
  `sett_code` varchar(10) DEFAULT NULL
  `source` varchar(255) DEFAULT NULL
  `date` timestamp NULL DEFAULT NULL
  `address` varchar(255) DEFAULT NULL
  `description` varchar() DEFAULT NULL
  `url` varchar() DEFAULT NULL
  `surface_land` decimal(19,2) DEFAULT NULL
  `surface_building` decimal(19,2) DEFAULT NULL
  `no_bedrooms` int(11) DEFAULT NULL
  `no_bathrooms` int(11) DEFAULT NULL
  `no_levels` int(11) DEFAULT NULL
  `no_guest_bathrooms` int(11) DEFAULT NULL
  `no_parking_lots` int(11) DEFAULT NULL
  `sale_price` decimal(19,2) DEFAULT NULL
  `state` varchar(2) DEFAULT NULL
  `municipality` varchar(10) DEFAULT NULL
  `latitude` decimal(12,9) DEFAULT NULL
  `longitude` decimal(12,9) DEFAULT NULL
  `property_type` int(11) DEFAULT NULL
  `property_class` int(11) DEFAULT NULL
  `vca_selected` int(11) DEFAULT NULL
  `creation_user` int(11) NOT NULL
  `creation_date` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp()
  `update_user` int(11) NOT NULL
  `update_date` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp()
```

- RESTFUL API de lectura/escritura/modificación/borrado


#### Tabla _SAIIA_

- Desarrollarla con los mismos campos de que la anterior, excepto por `source_id` y `vca_selected`
  
- RESTFUL API de lectura/escritura/modificación/borrado

**Nota: No confundir esta tabla con la de la sección [6.  BD_SAII](#BDSAII).

#### Tabla _SAIIB_

- Desarrollarla con los mismos campos de que la anterior

- _RESTFUL API_ de lectura/escritura/modificación/borrado

**Nota: No confundir esta tabla con la de la sección [6.  BD_SAII](#BDSAII).

<!-- #### Vistas/RESTFUL API (la solución que se proponga) -->

<!-- Para lectura en ventana de 210 días de todas las tablas anteriores. -->

### 2. BD_Settlements

### Tabla _settlements_

- Campos:

```
 `sett_code`       : varchar() DEFAULT NULL
 `polygon`         : varchar() DEFAULT NULL
 `centroid`        : varchar() DEFAULT NULL

```
- RESTFUL API de lectura/escritura/modificación/borrado


### 3. BD_Appraisals

- Desarrollar la BD con las tablas mínimas para almacenar el histórico de avalúos que registre
- Datos de avalúo (id, info del inmueble valuado, timestamp, valor del avalúo, varios más)
- Datos de usuario (ids)
- Comparables usados (ids)
- RESTFUL API de lectura/escritura/modificación/borrado
- DEFAULTs como NULL

### 5. BD_Usuarios

- Tabla para almacenar la información del usuario
  - id_user, name, user_type, package (todos varchar).
- Tabla para control de tokens
  - id_user, tokens, tokens_left
- _RESTFUL_API_ de lectura/escritura/modificación/borrado para ambas tablas
- DEFAULTs como NULL

### 6. BD_SAII{#BDSAII}

Las siguientes dos tablas concentran la información del
_Sistema de Administración de Información de Inmuebles (SAII)_
-ver más adelante en las secciones [7. SAIIA](#SAIIA) y [8. SAIIB](#SAIIB).
No confundir con las definidas en la sección
[1. BD_Comparables](#bd_comparables).

<!-- (### 7.-Sistema-de-Administración-de- Información-de-Inmuebles-(SAIIA)) -->


#### Tabla SAIIA

```

`id` bigint(20) NOT NULL AUTO_INCREMENT,
  `source_id` varchar(10) NOT NULL,
  `state` varchar(2) DEFAULT NULL,
  `type` varchar(3) DEFAULT NULL,
  `date_scrapping` date DEFAULT NULL,
  `code` varchar(15) DEFAULT NULL,
  `price` varchar(30) DEFAULT NULL,
  `priceValue` decimal(20,2) DEFAULT NULL,
  `currencyId` varchar(2) DEFAULT NULL,
  `currency` varchar(3) DEFAULT NULL,
  `countryId` varchar(1) DEFAULT NULL,
  `country` varchar(10) DEFAULT NULL,
  `location` varchar(500) DEFAULT NULL,
  `settlement` varchar(500) DEFAULT NULL,
  `street` varchar(500) DEFAULT NULL,
  `postalCode` varchar(6) DEFAULT NULL,
  `bedrooms` int(11) DEFAULT NULL,
  `fullBathrooms` int(11) DEFAULT NULL,
  `halfBathrooms` int(11) DEFAULT NULL,
  `constructionSize` int(11) DEFAULT NULL,
  `lotSize` int(11) DEFAULT NULL,
  `parkingSpaces` int(11) DEFAULT NULL,
  `yearBuilt` int(11) DEFAULT NULL,
  `floors` int(11) DEFAULT NULL,
  `latitude` decimal(12,8) DEFAULT NULL,
  `longitude` decimal(12,8) DEFAULT NULL,
  `status` varchar(50) DEFAULT NULL,
  `promotionDays` int(11) DEFAULT NULL,
  `statusId` varchar(3) DEFAULT NULL,
  `date_added` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `description` varchar() DEFAULT NULL
  `url` varchar() DEAFULT NULL

```

#### Tabla SAIIB

Similar a la anterior, pero por definirse en el levantamiento de requerimientos.
**Para tal efecto, se requiere que el desarrollador realice un scrapping inicial
para observar las llaves (suponiendo que se genera un JSON) y proporcionar una
muestra** (ver [Sección 8]({#SAIIB})).

### 7. Sistema de Administración de Información de Inmuebles A (SAIIA){#SAIIA}

#### *Antecedentes*

Actualmente se cuenta con un SAII, cuyo funcionamiento y operación requieren de mejoras en dos áreas:

- El programa cliente de recuperación de información en internet (PRII)

- La base de datos que almacena la información -ver [6. BD SAII](#BDSAII).

#### *Requerimientos*

1. Adecuar el PRII para que contenga:
    - Parámetros para manipular su funcionamiento, como son:
        1. Estado de la república por consultar
        2. Usuario y contraseña
        3. Tiempos entre ítem consultado
        4. Número de ítems a descargar
        5. Aspectos de la infraestructura que sean susceptibles de adecuación que reflejen los cambios en la misma en el site consultado.
        6. Correos electrónicos de mensajes de monitoreo
        
**Nota: estos parámetros también pueden incluirse como insumos de configuración.**

  - Sistema de log para monitoreo, que informe:
    
    1. Sobre la presencia de errores en los puntos de fricción en la interacción entre el PRII y la infraestructura
    2. Mensajes informativos sobre el error generado en cada punto de fricción.
    3. Envío de mensajes a lista de correos proporcionado como parámetro o como insumo de configuración.
    4. Archivo incremental de logs con fecha y hora de escritura.
        
    - Eliminar pasos intermedios innecesarios, como son ahora el acceso a la BD para almacenar y consultar información en bruto **(por aclararse en el levantamiento de requerimientos)**.

    - Proponer en lugar del paso intermedio mencionado anteriormente un sistema que permita almacenar en archivo de texto incremental la información en bruto recopilada o ciertas partes (por definir en conjunto con el desarrollador en los levantamientos de requerimientos). Acceder en cambio a la BD con la tabla definitiva. Ver propuesta para la misma en el punto siguiente.
    
    - Acceso a la BD mediante API, es decir, adecuar el código (parámetros de usuario y llave/password como insumo de configuración)

**Nota general: Actualmente se dispone de una tecnología en PHP, el requerimiento actual no supone un cambio en ésta.**

2. **Adecuar la BD para hacerla escalable y disponible mediante microservicios**

  - Eliminar la tabla que almacena la información recopilada en bruto.
  - Definir los campos de la tabla (ver [6. BD_SAII](#BDSAII)).
  - Definir usuarios de la DB y sus permisos.
  - Generar una API que permita:
      1. Lectura de registros por fecha o intervalo de fechas en conjunto con campos como estado y municipio (con opción de no especificarlos).
      2. Lectura por id.
      2. Escritura de registros nuevos.
      3. Modificación de registros por id.
      4. Borrado de registros por id.
      5. *Llenado automático* de campos administrativos: adición, última modificación, usuario de la adición/modificación.
      6. ¿Existe opción de congelado de registro en MYSQL/MariaDB? Si es así habilitar esta opción mediante la API para evitar borrar y simplemente congelar el registro.

  - Generar un sistema de backup a archivo de texto incremental diario, que descarte el primer día de la tabla y lo almacene en el archivo incremental, dejando vivos los registros en una ventana de 7 meses (210 días).

**Nota general: Actualmente se dispone para el desarrollo de la API una versión actualizable de Simfony; se puede proponer una versión actual o cualquier otra tecnología, siempre que se mencione explicítamente.**


### 8. Sistema de Administración de Información de Inmuebles B (SAIIB){#SAIIB}

#### *Antecedentes*

Este sistema es parecido al anterior de la seccion [7. SAIIA](#SAIIA), pero no se
encuentra desarrollado y **algunos particulares se resolverán en el levantamiento de requerimientos**.

#### *Requerimientos*

1. Se requiere del desarrollo de un SAII, que integre:

- El programa cliente de recuperación de información en internet (PRII)
- La base de datos que almacena la información - ver [6. BD SAII](#BDSAII).


2. Desarrollar el cliente PRII para que contenga:

  - Parámetros para manipular su funcionamiento, como son:
  
    1. Estado de la república por consultar
    2. Usuario y contraseña
    3. Tiempos entre ítem consultado
    4. Número de ítems a descargar
    5. Aspectos de la infraestructura que sean susceptibles de adecuación que
    reflejen los cambios en la misma en el site consultado.
    6. Correos electrónicos de mensajes de monitoreo
        
**Nota: estos parámetros también pueden incluirse como insumos de configuración.**

  - Sistema de log para monitoreo, que informe:
    1. Sobre la presencia de errores en los puntos de fricción en la interacción
    entre el PRII y la infraestructura
    2. Mensajes informativos sobre el error generado en cada punto de fricción.
    3. Envío de mensajes a lista de correos proporcionado como parámetro o como
    insumo de configuración.
    4. Archivo incremental de logs con fecha y hora de escritura.
        
  - Acceso a la BD mediante API, es decir, adecuar el código (parámetros de
  usuario y llave/password como insumo de configuración)

**Notas generales: Establecer una etapa para un estudio de factibilidad
(ver en observaciones) y proponer la tecnología a implementar.**

  2. Desarrollar la BD con características de escalabilidad y disponibilidad mediante microservicios

  - Definir los campos de la tabla (ver [6. BD_SAII](#BDSAII)).
  - Definir usuarios de la DB y sus permisos.
  - Generar una API que permita:
      1. Lectura de registros por fecha o intervalo de fechas en conjunto con
      campos como estado y municipio (con opción de no especificarlos).
      2. Lectura por id.
      2. Escritura de registros nuevos.
      3. Modificación de registros por id.
      4. Borrado de registros por id.
      5. *Llenado automático* de campos administrativos: adición,
      última modificación, usuario de la adición/modificación.
      6. ¿Existe opción de congelado de registro en MYSQL/MariaDB? Si es así
      habilitar esta opción mediante la API para evitar borrar y simplemente
      congelar el registro.
  
  - Generar un sistema de backup a archivo de texto incremental diario,
  que descarte el primer día de la tabla y lo almacene en el archivo
  incremental, dejando vivos los registros en una ventana de 7 meses (210 días).

**Nota general: Proponer framework/tecnología de desarollo para la API.**

  - Observaciones: Se dispone de un programa cliente de recuperación de la
  información para el SAIIA, que puede ser proporcionado para fines de estudio
  y adecuación para el desarrollo del programa cliente del SAIIB.

# Requerimientos para la presentación de la propuesta:

1. La propuesta debe presentar un presupuesto para el desarrollo y
su *documentación* por cada sección.

2. La propuesta debe presentar apartados de anticipio, avances y conclusión, como
sea necesario.

3. Generar sesiones por meet o zoom para aclaración de dudas y levantamiento
de requerimientos. En estas se despejarán, entre otras, dudas acerca del
SAII.
