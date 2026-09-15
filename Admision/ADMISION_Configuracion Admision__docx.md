Fuente original: Configuracion Admision.docx
Ruta original: Configuracion Admision.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image1.png" style="width:2.94792in;height:0.5in" />

**Configuración de módulo**

**Admisión**

**Versión 1.0**

# ***<span class="smallcaps">Información del Documento</span>***

|  | **Título del Documento** | Configuración de módulo: Admisión |
|:---|:---|:---|
| **Información General** | **Localización del documento** |  |

| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|:---|:---|:---|:---|:---|
|  | Catalina Claucich | Implementación | Análisis Funcional | 31/01/23 |
|  |  |  |  |  |

| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|:-----------------|----------------------|--------------|---------|:----------|
|                  |                      |              |         |           |
|                  |                      |              |         |           |

| **Aprobado por** | **Nombre (Empresa)**       | **Proceso** | **Rol** | **Fecha** |
|:-----------------|:---------------------------|:------------|:--------|:----------|
|                  |                            |             |         |           |
|                  | <span class="mark"></span> |             |         |           |
|                  | <span class="mark"></span> |             |         |           |
|                  |                            |             |         |           |
|                  | <span class="mark"></span> |             |         |           |

| **Documento** | **Versión** | **Motivo del Cambio** | **Fecha Efectiva** |
|:--------------|:------------|:----------------------|:-------------------|
| **Historia**  |             |                       |                    |
|               |             |                       |                    |
|               |             |                       |                    |

## <span class="indexref" entry="Objetivos: : : "></span>Objetivos

El presente documento tiene como objetivo principal informar la configuración del módulo de admisión.

## Alcance 

Se presentarán las configuraciones más relevantes del módulo de admisión, agrupadas en las siguientes secciones:

1.  sectores de admisión y puestos de trabajo.

2.  Función internación.

3.  Tipo internación.

4.  Tipo internación por servicio centro.

5.  Estructura edilicia de internación: sectores, ambientes y camas.

6.  Origen internación.

7.  Tipo de alta y destinos al alta.

8.  Pulseras y documentación.

9.  Orden de internación.

## Configuración: admisión Internados

1.  **Sectores de admisión y puestos de trabajo**

En Administración General » Configuración operativa » Centro Atención » Centro Atención se configuran parámetros importantes relacionados con la admisión.

- <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image2.png" style="width:6.26806in;height:2.93403in" />**Sector de admisión:** se deben crear los diferentes sectores abocados a la admisión de pacientes. Cada sector podrá tener sus correspondientes puestos de trabajo.

  <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image3.png" style="width:6.26806in;height:2.92292in" />

- **Puestos de admisión:** se deben definir los puestos de admisión correspondientes a cada sector de admisión. En esta instancia se pueden cargar parámetros relacionados con la operatoria del área: nombre de scanner y de impresoras, tipos de pulseras.

- <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image4.png" style="width:6.26806in;height:2.91319in" />**Caja sector admisión:** en esta sección se asocia cada sector de admisión a una caja, en caso de que correspondiera. Para realizar esta vinculación, tanto la caja como el sector tienen que estar previamente definidos.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image6.png" style="width:6.26806in;height:2.94097in" />

- **Personal sector admisión:** en esta sección se procede a cargar el personal que trabajará en cada sector de admisión.

- <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image7.png" style="width:6.26806in;height:2.96042in" />**Sector admisión tipo internación:** se configuran aquí los tipos de internación por servicio centro que se admisionarán en cada sector de admisión.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image8.png" style="width:6.26806in;height:2.91806in" />

- **Área reserva admisión:** se definen aquí las diferentes áreas que establecerán para la realización de reservas. Esto es completamente parametrizable por cada institución e impactará en la forma de ver las reservas en la pantalla de trabajo.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image9.png" style="width:6.26806in;height:2.88056in" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image10.png" style="width:6.26806in;height:2.93958in" />

- **Sector admisión área reserva:** se parametrizan aquí qué áreas de reserva corresponderá a cada sector de admisión y qué servicios abarcará.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image11.png" style="width:6.26806in;height:2.93611in" />Esto implica que, tomando a la última imagen como referencia, si se realiza una reserva de internación para el servicio Terapia Intensiva Adulto, la reserva se visualizará en la Admisión Principal, en el área de reservas Internación General Requisitorias.

2.  **Función Internación**

En Admisión internados » Configuración » Función internación se configura este parámetro. La función de internación relaciona las prestaciones de pensión o de terapia/rehabilitación que se cargan en cada cama e impactarán en facturación.

Estas prestaciones hacen referencia a:

**- Prestación Pensión:** prestación que es generada cada día de internación en los intervalos estipulados como Check In y Check Out durante el período de vigencia de la misma.

**- ARM/Incubadora/Luminoterapia/Óxido nítrico:** prestaciones que luego de ser habilitadas, se gestionan para su facturación a través de la registración de una fecha de inicio y fin en el censo gráfico. En general el inicio y fin de estas prestaciones está controlado por el área de enfermería.

Existe también un parámetro de “Nursery” con el fin de que las camas vinculadas a esta función no sean contabilizadas en el libro de internación.

Asimismo, la función puede tener asociados prestaciones o ítems con el fin de que los mismos sean generados para su facturación cada día con el mismo mecanismo de la prestación pensión.

3.  **Tipo Internación**

En Admisión internados » Configuración » Tipo Internación puede configurarse cada tipo de internación. Cada tipo de internación está relacionado con un tipo de admisión; el sistema de hospital contempla 3 tipos de admisiones posibles:

\- HOSPITALARIA

\- TRANSITORIA

\- AMBULATORIA

Crear un tipo de internación permite diferenciar la información estadística en, por ejemplo, internaciones clínicas o quirúrgicas. El tipo de internación también será utilizado en asociaciones posteriores. Los campos que pueden configurarse por tipo de internación se listan a continuación.

- **Requiere alta médica:** alta médica obligatoria para el egreso del paciente.

- **Requiere cama:** asignación obligatoria de una ocupación al realizar la admisión.

- **Requiere domicilio:** campo obligatorio al realizar la admisión.

- **Requiere institución derivante:** campo obligatorio al registrar una derivación externa.

- **Requiere motivo internación:** campo obligatorio al realizar la admisión.

- **Requiere responsable administración:** campo obligatorio al realizar la admisión.

- **Requiere libro internación:** este tipo internación genera registro en libro de internación.

- **Requiere diagnóstico al alta:** campo obligatorio para otorgar el alta.

- **Requiere convenio en reserva de internación:** campo obligatorio ingresar reserva.

- **Requiere epicrisis:** generación del alta mediante epicrisis obligatoria.

- **Requiere médico derivante:** campo obligatorio al realizar la admisión.

- **Requiere responsable internación:** campo obligatorio al realizar la admisión.

  \-**Requiere teléfono:** campo obligatorio al realizar la admisión.

4.  **Tipo Internación por Servicio Centro**

En Admisión internados » Configuración » Tipo de Internación por Servicio Centro se genera la asociación del Tipo de internación con la función de internación para cada servicio centro de internación.

Todas las prestaciones definidas en la función junto con los parámetros del tipo de internación serán gestionados para cada paciente admisionado en ese servicio de internados.

A su vez, en esta pantalla también se puede definir:

- **control de edad obligatorio y rango específico:** se restringe la admisión a dicho servicio si el paciente no se encuentra en el rango del control.

- **Control de sexo:** se restringe la admisión a dicho servicio si el paciente no corresponde al sexo especificado.

- **Porcentaje de reserva:** parámetro que permite llevar una alerta visual en las pantallas de “Disponibilidad Actual” y “Reservas Internación” cuando se supera el porcentaje de reservas definido.

- **Permite internar bebé:** check que permite habilitar la internación de recién nacidos en el servicio (Pacientes con tipo documento nomenclado como “BEBE…”.

- **Permite binomio:** check que permite la internación conjunta en el servicio.

5.  **Estructura edilicia de internación: sectores, ambientes y camas.**

- **Tipo de ambiente:** aquí se definen los diferentes tipos de ambiente de internación. Los ambientes son análogos a habitaciones o salas.

  Por ejemplo, puede definirse un tipo de ambiente llamado Habitación individual, que pertenecerá al área de internación:

- <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image12.png" style="width:6.26806in;height:2.94236in" />**Sector servicio internación:** se definen aquí todos los sectores de internación, que serán análogos a los pisos.

  En primer lugar se debe asignar un nombre y determinar el centro de atención al cual pertenece.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image13.png" style="width:6.26806in;height:2.95556in" />Luego, se debe especificar los servicios localizados en ese sector

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image14.png" style="width:6.26806in;height:2.92778in" />Para cada servicio del sector se puede especificar los depósitos asociados, la cantidad de horas de pedido de medicación, el tipo de impresora para la indicación farmacéutica, la forma de registración de consumo de ítems, entre otros.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image15.png" style="width:6.26806in;height:2.91667in" />

Por último, se deben signar los depósitos secundarios de cada servicio asociado al sector. En este lugar se configuran los depósitos como los carros de paro, es decir, depósitos de los cuales puede tomarse ítems para administrar a paciente, pero son distintos a aquel depósito al cual llegan las reservas de paciente.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image16.png" style="width:6.26806in;height:2.93611in" />

- **Ambiente de internación:** en esta sección se crean todos los ambientes de internación que existan en la institución. Para cada uno de ellos se debe asignar un nombre, tipo de ambiente y seleccionar un sector de internación. Este último parámetro traerá asociado: función de internación, tipo de admisión, tipo de internación, centro de atención y servicio. Además debe determinarse el número máximo de camas del ambiente.

  <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image17.png" style="width:6.26806in;height:2.94931in" />Asimismo, en esta sección puede gestionarse el estado del ambiente y de las camas.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image18.png" style="width:6.26806in;height:2.92292in" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image19.png" style="width:6.26806in;height:2.93264in" />

6.  **Origen internación**

El origen de internación hace referencia a desde dónde surge la internación. Por ejemplo, puede ser libre, programada, derivación, etc. Los orígenes de internación dependerán de la forma de trabajo de la institución y se utilizarán a la hora de admisionar a un paciente.

Para crear un origen de internación se debe asignar un nombre, número de orden y tipo de origen de internación.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image20.png" style="width:6.26806in;height:2.92639in" />

7.  **Tipo de alta y destino al alta**

En Administración general » Dominios médicos » Tipo alta puede configurarse todos los tipos de alta y destinos al alta con los que trabaje la institución.

En primer lugar, se deberá crear un tipo de alta, asignando nombre, ámbito (ambulatorio o internado), si es óbito, derivación interna o binomio.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image21.png" style="width:6.26806in;height:2.93611in" />Una vez creado el tipo de alta, pueden añadirse formularios para completar al alta.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image22.png" style="width:6.26806in;height:2.90347in" />Estos se crean en Administración general » Dominios médicos » Formularios historia clínica » Formularios médicos. Para que los formularios puedan visualizarse al dar el alta tienen que crearse con el el tipo de formulario “Tipo alta”.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image23.png" style="width:6.26806in;height:2.94931in" />

Por último, deben asignarse los destinos al alta asociados al tipo de alta creado. Para ello se otorga un nombre y ámbito de aplicación a cada tipo de alta.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image24.png" style="width:6.26806in;height:2.89375in" />

8.  **Pulseras y documentación**

Existe la posibilidad de definir distintos tipo de documentos y pulseras que se podrán imprimir en el contexto de una admisión.

- **Tipo pulsera:** en Admisión » Configuración » Tipo pulsera pueden crearse distintos tipos de pulsera, según corresponda. Por ejemplo, adulto, pediátrico y neo. Para cada uno se debe asignar un nombre, el tamaño (alto y largo en mm) y las características del trazo (grosor y distancia de la línea en mm).

  <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image25.png" style="width:6.26806in;height:2.91667in" />Asimismo, puede especificarse qué campos saldrán impresos en la pulsera, junto con la ubicación de cada uno de ellos y las características del texto.

  <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image26.png" style="width:6.26806in;height:2.90694in" />Una vez creados los tipos de pulsera se asignarán en cada puesto de admisión, tal como se explicó en el punto 1.

- **Documentación admisión:** en Administración general » Configuración operativa » Modelos documentación » Modelos documentación admisión se pueden configurar los documentos adicionales que se imprimirán en la admisión. Ueden hacer referencia a normas de internación, declaración de alergias, consentimientos informados, etc.

  Estos formularios son creados íntegramente a demanda, con la posibilidad de crear distintos formularios para diferentes centros de atención y tipos de internación por servicio centro.

  <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image27.png" style="width:6.26806in;height:2.90486in" />Para crear un formulario se debe asignar nombre y código, título, centro de atención y un modelo de documento (que puede ser modificado con posterioridad). Un vez que ya se ha creado el modelo de un documento, se pueden asignar los tipos de internación por servicio centro para los cuales aplicará.

  <img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image28.png" style="width:6.26806in;height:2.93611in" />

9.  **Orden de internación**

La carga de la orden de admisión se realiza en los datos de internación del paciente. Puede añadirse una orden desde cero, o utilizando formularios predefinidos. Esta última opción es muy útil cuando existen órdenes estándares que pueden replicarse a varios pacientes.

La creación de una orden de internación se realiza en Facturación internados » Configuración » Modelo orden de internación. Se debe asignar nombre y convenio de manera obligatoria. De manera opcional, puede cargarse patología y observaciones.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image29.png" style="width:6.26806in;height:2.94236in" />En la sección “Detalles” se puede configurar la cobertura que se autorizó en esa orden de servicio.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/media/image30.png" style="width:6.26806in;height:2.92292in" />
