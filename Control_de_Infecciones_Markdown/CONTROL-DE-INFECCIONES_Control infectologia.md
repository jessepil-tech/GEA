Fuente original: Control infectologia.odt
Ruta original: Control de infecciones/Control infectologia.odt
Formato original: ODT

En esta sección se describe el control de infectología que puede realizarse en la aplicación. Se aborda también la colocación y extracción de dispositivos.

#### control de infecciones

En este apartado se describen las funcionalidades y procesos involucrados en el control de infecciones.

##### microorganismos

En la aplicación pueden definirse diferentes microorganismos, que podrán ser seleccionados al momento de realizar los diferentes registros relacionados con este módulo.

Para ello, se debe acceder a Control de infecciones » Configuración » Microorganismo:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F0000039242EE9B8255BEC952.png" style="width:17cm;height:8.096cm" />

Allí se definirá el nombre del microorganismo, su número de orden y si emite alerta o no. Si el check “Emite alerta” se encuentra activado, los aislamientos que tengan el microorganismo asociado se visualizarán en determinadas consultas y generarán un registro de advertencia:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F000003CA01D0F7BBBA9315A0.png" style="width:17cm;height:8.116cm" />

##### Tipo de aislamiento

Asimismo, pueden especificarse los distintos tipos de aislamientos que se contemplarán en la institución. Para ello se debe ingresar a Control de infecciones » Configuración » Tipo Aislamiento:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F00000390A0593EDD0685FFAA.png" style="width:17cm;height:8.079cm" />

Los parámetros a determinar son: nombre del tipo de aislamiento, si se encuentra activo o no, y el color que lo representará en el censo gráfico y en la planilla de control de infecciones.

##### Registro de infecciones

En el sistema se brinda una funcionalidad al personal de infectología que permite visualizar los pacientes internados y determinar si alguno de ellos requiere aislamiento.

La consulta en cuestión se encuentra en Control de infecciones » Infectología » Infectología:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F0000039071A7791B941DC514.png" style="width:17cm;height:8.079cm" />

Como se indica en la imagen, se puede obtener un listado de pacientes internados por sector y centro de atención, en el cual se visualizará el tipo de aislamiento que cada paciente presenta. Asimismo se puede iniciar o finalizar aislamientos, según corresponda.

Al iniciar un aislamiento, se solicita ingresar la fecha de inicio, el tipo de aislamiento y el microorganismo. De manera opcional pueden añadirse observaciones.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F0000038C9793129B2117D1AF.png" style="width:17cm;height:8.043cm" />

Cuando se asigne un aislamiento se visualizará la información correspondiente en el censo gráfico:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F0000038EF401305716DA2807.png" style="width:17cm;height:8.061cm" />

Al finalizar un aislamiento, el sistema solicitará confirmación de la acción:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077E000003806D245094BA82B3F1.png" style="width:17cm;height:7.941cm" />

Una vez finalizado el aislamiento, el paciente se visualizará nuevamente sin ningún color particular.

#### Consultas

A continuación se presentan las distintas consultas disponibles en la aplicación para obtener información en relación al control de infecciones.

##### Consulta de aislamientos

En la aplicación existe una consulta que permite conocer cuál es el estado de aislamiento de los pacientes de un sector para una fecha en particular. La consulta en cuestión se encuentra en Control de infecciones » Infectología » Consulta infectología.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F0000034EF30BD1BDCB5CAAD6.png" style="width:17cm;height:7.493cm" />

Se permite filtrar por aislamiento vigente, no vigente, todos. En aquellos casos en que el aislamiento se haya finalizado se indicará fecha y hora de fin, y personal que finaliza.

##### Consulta generador de censo

En Control de infecciones » Consultas » Consulta generador de censo puede accederse a la consulta homónima de admisión. Allí se brinda información relacionada con el aislamiento de cada paciente.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F00000390A46DAA0EE4CAA5CA.png" style="width:17cm;height:8.079cm" />

##### Consulta de alertas

En Control de infecciones » Consultas » Consulta de alertas se muestra el listado de aislamientos que tuvo o tiene un paciente, en los cuales se asignó un microorganismo que tenía el check “Emite alerta” tildado.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000077F0000038C82890FF2494800A7.png" style="width:17cm;height:8.043cm" />

#### **Colocación, extracción y visualización de dispositivos**

El personal de enfermería puede registrar la colocación y extracción de dispositivos en un paciente internado. Esta tarea se realiza desde el módulo de enfermería, según el tipo de enfermería configurado para cada caso.

Es posible diferenciar cada dispositivo por código de identificación y color. Asimismo, el esquema de colocación varía si el paciente es adulto/pediátrico o neonato, como puede verse a continuación.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/10000000000007360000020046E57A9DDB8CC332.png" style="width:14.208cm;height:3.941cm" />

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/1000000000000724000001CD7DFDAB5A5BF61963.png" style="width:14.162cm;height:3.572cm" />

A la hora de eliminar un dispositivo, se visualizará un pop up en cual se debe indicar la fecha de extracción y confirmar la acción:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/1000000000000589000002853D5AF585C24DE38E.png" style="width:17cm;height:5.653cm" /><img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/1000000000000590000001C699F5D1A3786B8BC3.png" style="width:17cm;height:5.419cm" />

Cuando se haya extraído un dispositivo, se seguirá visualizando en la pantalla pero aparecerá tachado, tal como sucede con una indicación médica suspendida.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/1000000000000585000001CEF1E222178DB1E38B.png" style="width:17cm;height:5.558cm" />

En esta pantalla, en el ícono , también puede visualizarse información relacionada con la colocación y extracción de cada dispositivo:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/1000000000000190000000F0863ACB8A1A67CF81.png" style="width:4.697cm;height:2.819cm" />

Para observar la información de manera más completa se modificará la pantalla inicial:

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/Pictures/100000000000058B000001DCB497C56AC8A329AC.png" style="width:17cm;height:5.703cm" />

#### **Consultas**

Con el objetivo de visualizar el histórico de colocación de dispositivos, se añadirán consultas. Las mismas podrán ser accedidas por personal médico y de enfermería, a nivel de episodio de internación y de historia clínica del paciente.

Consulta Infectología

Esta consulta se localizará en Control de infecciones \>\> Consultas. Se llamará "Consulta dispositivos".

Contará con los siguientes campos:

\* Paciente (nombre y apellido). Se mostrarán los pacientes internados al momento de búsqueda. Cuando el paciente tenga alta administrativa desaparecerá de la consulta.

\* Tipo de documento.

\* Número de documento.

\* Servicio.

\* Sector de internación.

\* Cama.

\* Nombre dispositivo.

\* Fecha de colocación.

\* Personal coloca.

\* Fecha de extracción.

\* Personal extrae.

\* Observaciones.

Asimismo, existirán los siguientes filtros:

\* Paciente;

\* Dispositivo;

\* Sector;

\* Fecha de colocación desde y Fecha de colocación hasta.

Consulta histórico

En el episodio de internación de cada paciente se visualizará la misma consulta que se muestra en la pantalla de enfermería internados.
