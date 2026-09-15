Fuente original: Info admision.odt
Ruta original: Info admision.odt
Formato original: ODT

#### MÓDULO ADMISIÓN

El presente documento tiene como finalidad explicar todos los procesos y la configuración correspondiente al módulo de Admisión de Internados.

En el alcance del proyecto se deberá configurar la estructura completa de todos los sectores de internación, la documentación necesaria para gestionar un ingreso y todo parámetro administrativo que se releve necesario para la internación de un paciente.

Se dejarán descriptas también de forma general, todas las consultas de gestión disponibles para los procesos que intervienen en este módulo.

#### **1. **[**Configuración**](#_Toc81563833)** Admisión**

Servicio

Se define el nombre del servicio de internación. Esto se determina en Admisión internados » Configuración » Servicio.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000003530000009DAF1284C881A9D1A7.png" style="width:15cm;height:2.768cm" />

Servicio Centro

Definiciones generales propias de cada servicio por centro de atención, en Admisión internados » Configuración » Servicio Centro.

Deben indicarse de forma obligatoria los parámetros de:

- Centro de atención al que pertenece el servicio a definir.
- Empresa y sucursal del servicio-centro.
- Si el servicio es únicamente ambulatorio, internado o de ambos.
- Tipo de servicio (referente a donde será dirigido el uso del servicio según el modulo a utilizarse).

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000076200000326D0455C26E460006B.png" style="width:15cm;height:6.398cm" />

En la pestaña *Internación* se determinan parámetros específicos de servicios de internación, como leyenda en la orden de servicio de internados, si es un servicio que interna pacientes (parámetro también indicado a nivel de centro de atención), etc.

Función Internación

En Admisión internados » Configuración » Función internación puede configurarse cada Función internación, que luego podrá ser asociada a diferentes tipos de prestaciones fundamentales en los distintos tipos de internación.

Estas prestaciones hacen referencia a:

- *Prestación Pensión:* prestación que es generada cada día de internación en los intervalos estipulados como Check In y Check Out durante el periodo de vigencia de la misma.
- *ARM-INCUBADORA-LMT (Luminoterapia)-OXIDO NITRICO:* prestaciones que luego de ser habilitadas, se gestionan para su facturación a través de la registración de una fecha de inicio y fin en el censo gráfico.

Existe también un parámetro de “Nursery” con el fin de que las camas vinculadas a esta función no sean contabilizadas en el libro de internación.

Por otro lado, la función puede conllevar asociadas/os prestaciones/ítems con el fin de que los mismos sean generados para su facturación cada día con el mismo mecanismo de la prestación pensión.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F0000022672EEB1E21D8B1B21.png" style="width:15cm;height:4.299cm" />

Tipo Internación

En Admisión internados » Configuración » Tipo Internación puede configurarse cada tipo de internación.

El sistema de hospital contempla 4 tipos de admisiones posibles:

- HOSPITALARIA
- TRANSITORIA
- AMBULATORIA
- DOMICILIARIA

Se crea para cada una de las anteriores un tipo de internación que permita diferenciar, mediante una asociación posterior, los siguientes parámetros para cada servicio de internación:

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F0000014E0F61D8C3BF61B40B.png" style="width:15cm;height:2.611cm" />

1.  **Requiere Alta Médica:** alta médica obligatoria para la desocupación de la ocupación.
2.  **Requiere Cama:** asignación obligatoria de una ocupación al realizar la admisión.
3.  **Requiere domicilio:** campo obligatorio al realizar la admisión.
4.  **Requiere Institución Derivante:** campo obligatorio al registrar una derivación externa.
5.  **Requiere Motivo Internación:** campo obligatorio al realizar la admisión.
6.  **Requiere Responsable Administración:** campo obligatorio al realizar la admisión.
7.  **Requiere Libro Internación:** este tipo internación genera libro de internación.
8.  **Requiere diagnostico al alta:** campo obligatorio para otorgar el alta.
9.  **Requiere Convenio en Reserva de internación:** campo obligatorio ingresar reserva.
10. **Requiere Epicrisis:** generación del alta mediante epicrisis obligatoria.
11. **Requiere Médico Derivante:** campo obligatorio al realizar la admisión.
12. **Requiere Responsable Internación:** campo obligatorio al realizar la admisión.
13. **Requiere Teléfono:** campo obligatorio al realizar la admisión.

Tipo Internación por Servicio Centro

En Admisión internados » Configuración » Tipo de Internación por Servicio Centro se genera la asociación del Tipo de internación con la función en cada servicio centro de internados. Entrando en detalles, todas las prestaciones definidas en la función junto con los parámetros del tipo de internación serán gestionados para cada paciente admisionado en ese servicio de internados.

A su vez, en esta pantalla también se puede definirse:

- **Control de Edad obligatorio y su rango específico:** se restringe la admisión a dicho servicio si el paciente no se encuentra en el rango del control.
- **Control Sexo:** se restringe la admisión a dicho servicio si el paciente no corresponde al sexo especificado.
- **Porcentaje de Reserva:** parámetro que permite llevar una alerta visual en las pantallas de “Disponibilidad Actual” y “Reservas Internación” cuando se supera el porcentaje de reservas definido.
- **Permite Internar Bebé:** check que permite habilitar la internación de recién nacidos en el servicio (Pacientes con tipo documento nomenclado como “BEBE…”.
- **Permite binomio:** check que permite la internación conjunta en el servicio.

Se parametrizan aquí también, en caso de ser necesarias, las garantías y/o pagos a cuenta que se solicitaran al momento de admisionar un paciente.

Existen 2 tipos:

- **Garantía por Tipo de Internación:** montos a informar y cobrarse según el tipo de internación.
- **Garantía Tipo de Internación Convenio:** montos a informar y cobrarse según el convenio y tipo de internación.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F000001A09A1D2B6A85B6B5AD.png" style="width:15cm;height:3.253cm" />

Tipo Ambiente

Creación de los distintos tipos de ambientes (ocupaciones) que existen en la estructura del área de internación. Esto se realiza en Admisión internados » Configuración » Tipo ambiente.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000003D0000000E35D4D18AF19DEDBF4.png" style="width:15cm;height:3.487cm" />

Sector Servicio Internación

En Admisión internados » Configuración » Sector Servicio Internación puede delimitarse por sector la estructura del área de internación según los servicios y office-enfermería para un Centro de Atención.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000032E0000010B8C15C2ABDC8EDBD3.png" style="width:15cm;height:4.919cm" />

Se brinda la posibilidad de definir puntos importantes para cada servicio en su sector:

- Depósito de pedido de Indicación Farmacia
- Depósito de pedido Fuera de Horario
- Deposito Sector Internación
- Cantidad de Horas Pedido Medicación (Parámetro para pedido manual)
- Forma de registrar consumo (Por devolución o por Planilla de Enfermería)
- Cantidad de horas previas a notificar Indicacion por vencer
- Genera Housekeeping (Habilitacion del circuito del Housekeeping)

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077100000123D90D657A5593E2AF.png" style="width:15cm;height:2.291cm" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000036A000001E80E4AB9E83E607D5B.png" style="width:7.721cm;height:4.311cm" />

Si existieran, también pueden agregarse los depósitos secundarios involucrados en los procesos de cada servicio del sector.

Ambiente Internación

En Admisión internados » Configuración » Ambiente Internación se realiza la definición de las ocupaciones especificas de cada servicio de internación (según función y tipo de internación).

Se le asigna: una descripción a modo de nombre, el tipo de ambiente de la ocupación, nro de orden en torno a la visualización en el censo gráfico y la cantidad de camas que contendrá el mismo.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000039C0000020C265CBFE53D7949B0.png" style="width:8.31cm;height:4.711cm" />

Las camas pueden ser visualizadas desde esta opción de configuración pero son agregadas en el censo gráfico.

Se debe ingresar al sector de internación respectivo y desde el botón superior derecho de cada Ambiente, se selección la opción “Agregar Cama”. Se controlara la cantidad de camas máximas por ambiente y se solicitara un nombre para cada Cama agregada.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000002620000013580F37BA015333B9A.png" style="width:6.161cm;height:3.12cm" />

Origen Internación

En Admisión internados » Configuración » Origen Internación se pueden crear los diferentes orígenes de internación que derivan en una admisión. Este es un parámetro no obligatorio a completarse durante el proceso administrativo de internación.

Algunos ejemplos pueden ser: Guardia, Derivación Externa, Programada, Nacimiento, etc.

Tipo Pulsera

En Admisión internados » Configuración » Tipo Pulsera se definen los tipos de pulsera de internación a generarse al momento de una admisión.

Se deben ingresar las medidas longitudinales para luego trabajar con diferentes marcadores en ejes de coordenadas con el fin de asignarles una posición en la impresión, que luego será enviada a la impresora de pulseras.

Su parametrización es dinámica según la cantidad de información que se desea visualizar en la pulsera. Contempla desde pulseras simples hasta de Binomio (4 pulseras).

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000002F7000000CDC97B5563A3CFCF85.png" style="width:9.327cm;height:2.519cm" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000066300000129B2CE990D325AD775.png" style="width:15cm;height:2.723cm" />

Modelos Documentación Admisión

Cada internación generada posee documentación a definirse en Administración general » Configuración operativa » Modelos Documentación » Modelo Documentación Admisión.

La documentación debe asignarse para cada tipo de internación según la necesidad y se pondrá a disposición para imprimirse al momento de efectivizarse el trámite de admisión.

Esta contempla documentación que puede llevar marcadores (campos que se completaran con datos cargados durante la admisión), como también documentos preformados y estructurados que pueden ser importados de un directorio raíz.

Los marcadores que puede contener este modelo de documentación son:

|                                  |                                 |
|----------------------------------|---------------------------------|
| DATO                             | MARCADOR                        |
| Centro Atención:                 | CENTRO_ATENCION                 |
| Servicio:                        | SERVICIO                        |
| Tipo Internación:                | TIPO_INTERNACION                |
| Nro. Internación:                | NRO_INTERNACION                 |
| Fecha Internación:               | FECHA_INTERNACION               |
| Hora Internación:                | HORA_INTERNACION                |
| Convenio:                        | CONVENIO                        |
| Plan Convenio:                   | PLAN_CONVENIO                   |
| Tipo Afiliado:                   | TIPO_AFILIADO                   |
| Nro. Afiliado:                   | NRO_AFILIADO                    |
| Motivo Internación:              | MOTIVO_INTERNACION              |
| Tipo Matricula Derivante:        | TIPO_MAT_DERIVANTE              |
| Nro. Matricula Derivante:        | NRO_MAT_DERIVANTE               |
| Médico Derivante:                | DERIVANTE                       |
| Sector Internación:              | SECTOR                          |
| Ambiente Internación:            | AMBIENTE                        |
| Nro. de Cama:                    | NRO_CAMA                        |
| Apellido Paciente:               | APELLIDO_PACIENTE               |
| Nombre Paciente:                 | NOMBRE_PACIENTE                 |
| Sexo Paciente:                   | SEXO_PACIENTE                   |
| Apellido y Nombre Paciente:      | APELLIDO_NOMBRE_PACIENTE        |
| Tipo Doc. Paciente:              | TIPO_DOC_PACIENTE               |
| Nro. Doc. Paciente:              | NRO_DOC_PACIENTE                |
| Teléfono Paciente:               | TE_PACIENTE                     |
| Fecha Nac. Paciente:             | FECHA_NAC_PACIENTE              |
| Apellido Responsable:            | APELLIDO_RESPONSABLE            |
| Nombre Responsable:              | NOMBRE_RESPONSABLE              |
| Tipo Doc. Responsable:           | TIPO_DOC_RESPONSABLE            |
| Nro. Doc. Responsable:           | NRO_DOC_RESPONSABLE             |
| Nro. Tel. Responsable:           | TEL_RESPONSABLE                 |
| Parentesco:                      | PARENTESCO_RESPONSABLE          |
| Domicilio Responsable:           | DOMICILIO_RESPONSABLE           |
| Persona de Contacto:             | PERSONA_CONTACTO                |
| Teléfono de Contacto:            | TE_PERSONA_CONTACTO             |
| Observación de Contacto:         | OBS_PERSONA_CONTACTO            |
| Admisionista:                    | ADMISIONISTA                    |
| Apellido Acompañante:            | APELLIDO_ACOMPAÑANTE            |
| Nombre Acompañante:              | NOMBRE_ACOMPAÑANTE              |
| Teléfono Acompañante:            | TE_ACOMPAÑANTE                  |
| Relación Parentesco Acompañante: | RELACION_PARENTESCO_ACOMPAÑANTE |
| Tipo Matricula Recibe:           | TIPO_MAT_RECIBE                 |
| Nro. Matricula Recibe:           | NRO_MAT_RECIBE                  |
| Médico recibe:                   | MEDICO_RECIBE                   |
| Edad del paciente:               | EDAD_PACIENTE                   |
| Calle del paciente:              | CALLE_PACIENTE                  |
| Número calle:                    | NRO_CALLE_PACIENTE              |
| Localidad:                       | LOCALIDAD                       |
| Domicilio                        | DOMICILIO_PACIENTE              |
| Calle y número domicilio:        | DOMICILIO_COMPLETO_PACIENTE     |
| Número HC Anterior:              | NRO_HISTORIA_CLINICA_ANTERIOR   |
| Logo centro de atención          | LOGO                            |
| Logo small centro atención:      | LOGO_SMALL                      |
| Condición IVA Internación        | COND_IVA_FACT                   |
| Origen de Internación            | ORIGEN_INTERNACION              |

Cualquiera de los logos requeridos debe estar cargado en el centro de atención.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000003C4000001D1AAE04CB2FDBAA7A1.png" style="width:11.252cm;height:5.427cm" />

Sector Admisión por Centro de Atención

En cada centro de atención deberán definirse los sectores de admisión equivalentes a su existencia física. Estos hacen referencia a las áreas donde se brinda/realiza la gestión administrativa de la internación al paciente. La configuración se realiza en Administración general » Configuración operativa » Centro atención » Centro atención, en la pestaña “Sector Admisión” del menú lateral.

Puesto de Admisión

Cada sector de admisión podrá tener asignado cuantos puestos de atención existan físicamente. Estos tendrán asociados y definidos los diferentes elementos de hardware periférico (impresoras láser, impresoras de pulseras, escáneres, impresora de etiquetas y de alergias). La configuración se realiza en Administración general » Configuración operativa » Centro atención » Centro atención, en la pestaña “Puesto Admisión” del menú lateral.

Caja Sector Admisión

Aquí se vincula la caja que se utilizará para el cobro de pagos adelantados y garantías en cada sector de admisión. La configuración se realiza en Administración general » Configuración operativa » Centro atención » Centro atención, en la pestaña “Caja Sector Admisión” del menú lateral.

Personal Sector Admisión

Debe definirse todo el personal administrativo que interviene en cada sector de admisión. La configuración se realiza en Administración general » Configuración operativa » Centro atención » Centro atención, en la pestaña “Personal Sector Admisión” del menú lateral.

Sector Admisión Tipo Internación

Asignación de los tipos de internación y servicio que podrán gestionarse en ese sector de admisión. Esto permite restringir por configuración las admisiones que puedan manejarse según, por ejemplo, si es un sector de admisión principal que interactúa con todos los tipos de internación, o un sector de admisión transitoria que solo pueda manejar las internaciones de ese tipo (como las de guardia). La configuración se realiza en Administración general » Configuración operativa » Centro atención » Centro atención, en la pestaña “Sector Admisión Tipo Int.” del menú lateral.

Área Reserva Admisión - Sector Admisión Área Reserva

La pantalla principal del admisionista puede estar definida con bloques de reserva configurables que permitan definir donde deberían caer las solicitudes de cama respectivas de cada servicio de internación.

En las áreas se definen los bloques, que se visualizarán luego en pantalla.

En los sectores de admisión de las áreas se definen cada uno de los servicios de internación que estarán clasificadas sus solicitudes dentro de ese bloque de reserva.

Las configuración se realizan en Administración general » Configuración operativa » Centro atención » Centro atención, en las pestañas “Área Reserva Admisión” y “Sector Admisión Área Reserva” del menú lateral.

#### 2. Checklist

CHECKLIST de configuración “ADMISION”:

1.  Servicios de Internación
2.  Función de Internación y sus prestaciones asociadas (Ej: Pensión).
3.  Tipos de Ambientes de Internación (Ocupaciones físicas)
4.  Tipos de internación (con garantías y pagos adelantados)
5.  Servicios por Sectores de Internación.
6.  Ambientes de Internación
7.  Tipo de pulsera
8.  Orígenes de Internación
9.  Modelos de Documentación de internación (Consentimientos, Normas, Caratula, etc).
10. Sectores de Admisión, puesto, caja y personal por centro de atención.

#### **3. proceso de admisión del paciente**

La admisión comienza con el apersonamiento del paciente, con una solicitud que puede ser o no programada (ya sea de servicios internos o de derivaciones externas).

El admisionista posee una pantalla de trabajo segmentada en navegadores que le permiten no solo gestionar un ingreso, sino también visualizar la disponibilidad actual de todas las ocupaciones, egresos esperados, ingresar nuevas reservas, gestionar asignación de interconsultas o traslados para estudios externos.

En la pantalla principal (Admisión » Admisión » Admisión Internados) se visualiza la configuración de las áreas definidas con antelación.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F0000039CA119688F4DE6E8E9.png" style="width:15cm;height:7.221cm" />

La identificación del paciente es de suma importancia, debido a que una identificación incorrecta produce la segmentación de la HC (evento que estará aislado de los anteriores), independientemente de que luego pueda unificarse a futuro si es detectada esta situación. En esta instancia se dispone de la lectura del DNI como única fuente para la confirmación automática de los datos del paciente.

Si el paciente posee una solicitud de internación existente, se gestionará su ingreso a través de la misma, verificando y agregando la información requerida. Si no posee solicitud de internación previa, es posible realizar un ingreso sin reserva.

Con los datos del financiador, se realizan las validaciones correspondientes, que establecen puntos de control posteriores, como la documentación obligatoria por convenio o los modelos de las órdenes de internación a ingresar.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F0000038ECA3B499DB7D050CD.png" style="width:15cm;height:7.114cm" />

La admisión posee varios parámetros de gestión en el proceso, tanto obligatorios como opcionales, que se describen a continuación:

- ***Datos del paciente:*** datos principales del paciente (filiatorios, mail, domicilio).

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000066E0000017F3FF3871C339D7F39.png" style="width:15cm;height:3.491cm" />

- **Teléfonos:** dato de contacto telefónico del paciente.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000066C0000013A815268DFCCBE48DD.png" style="width:15cm;height:2.865cm" />

- **Datos internación:** se deben ingresar los datos administrativos más relevantes para generar la admisión, como tipo de internación, función del servicio, origen de la internación, el médico que solicita y el que recibe al paciente, motivo y la declaración de alergia. Este último parámetro permite, en caso de ser declarada, imprimir una pulsera de color característico para que se defina el tipo de alergia durante la internación).

  Opcionalmente puede agregarse una persona de contacto.

- **Datos internación Conjunta:** permite visualizar un vínculo de internación conjunta o generar uno nuevo (controlando que las internaciones sean independientes una de la otra).

> 

- **Responsables:** asignación del responsable de la internación y de los cargos percibidos a facturarse (que no se encuentren cubiertos por el financiador).

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000066E000002A1A7C6027B5D98D4AE.png" style="width:14.626cm;height:5.98cm" />

- **Acompañante:** asignación y definición del vínculo del acompañante con el paciente.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000668000000B550FEFE4600534C0A.png" style="width:15cm;height:1.655cm" />

- **Documentación:** importación de toda la documentación relevante del paciente y de la internación. Puede escanearse o ser cargada de una carpeta raíz.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000670000002E34462A73E760DDC89.png" style="width:13.145cm;height:5.893cm" />

- **Documentación Requerida por Plan Convenio:** enumeración de la documentación obligatoria a presentarse definida en la parametrización de cada financiador.

- **Lugar internación:** opcionalmente, se puede asignar la ocupación al paciente al momento de realizar la internación. Para ello se solicitará fecha y hora, junto con la selección de cama del listado de camas disponibles. Si no hay camas disponibles del servicio de internación solicitado para el paciente, se podrá habilitar selección dentro de la disponibilidad de todos los demás servicios.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000067B000001E855F99F6B8C8E1976.png" style="width:15cm;height:4.413cm" />

- **Orden de internación:** si el paciente dispone de la orden de internación, podrá cargarse con el fin de contemplar todas las coberturas. La carga de la orden puede hacerse posterior al registro de la admisión y gestionarse o no, a través de modelos ya preformados.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000670000003020B918D7394162BEF.png" style="width:14.69cm;height:6.863cm" />

- **Extras Previstos:** carga de los extras que luego serán efectivizados para su correspondiente facturación posterior.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000066F0000007D45D1D1A9B890DD83.png" style="width:15cm;height:1.139cm" />

- **Datos Policiales:** datos específicos a ingresar si la admisión es derivada de algún siniestro o evento con intervención policial.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000026300000091CFDA241F15D73CEF.png" style="width:11.917cm;height:2.828cm" />

Al finalizar el ingreso de todos los datos relevantes se confirma la admisión, dando lugar a la posibilidad de realizar la impresión de las pulseras de internación y toda documentación previamente definida que formara parte final en la confirmación de la gestión administrativa.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000003F5000001102133096D5578D374.png" style="width:14.753cm;height:3.962cm" />

#### **4. Reserva de Internación**

El paciente puede solicitar la reserva de la internación en caso de ser una internación programada. Para ello se solicitan los siguientes datos en Admisión » Admisión » Reserva internación:

- fecha desde y fecha hasta;
- tipo de admisión, tipo internación, servicio y función (función de internación);
- convenio y plan, tipo y nro. afiliado (opcional);
- dato identificatorio de paciente. Si no existe en la base de datos, puede ingresarse como nuevo con: tipo y número de documento, apellido y nombre, fecha de nacimiento y sexo.

Se podrá observar la disponibilidad actual antes de ingresar la reserva, visualizando allí también, con una referencia de color, los servicios que tienen definido un porcentaje de reserva como alarma entorno a la ocupación de camas.

Con esto quedará registrada la reserva, que luego será visualizada según parámetros en las áreas de reserva de la pantalla principal del admisionista, para gestionar su ingreso el día indicado.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000774000001E1B1CDAB1A3DDC26F9.png" style="width:15cm;height:3.782cm" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000005DB000002757612EC6604BD3C7C.png" style="width:13.653cm;height:5.727cm" />

#### **5. Ingreso de derivación externa**

El sector a cargo de registrar las derivaciones puede ingresar una derivación externa desde Admisión » Solicitud de Internación Externa » Solicitud de Internación Externa, ingresando los siguientes datos:

- fecha desde y fecha hasta;
- tipo de admisión, tipo internación, servicio y función (función de internación);
- convenio y plan, tipo y nro. afiliado (opcional);
- dato identificatorio de paciente. Si no existe en la base de datos, puede ingresarse como nuevo con: tipo y número de documento, apellido y nombre, fecha de nacimiento y sexo;
- institución derivante;
- cantidad de días estimados de internación;
- motivo de derivación/diagnóstico;
- persona y dato de contacto – observación (opcionales).

Una vez registrada la solicitud, puede anularse la misma por pedido del derivante, o registrarse un rechazo en su autorización, ya sea por falta de cama, decisión médica, etc. Esto se realiza desde Admisión » Solicitud de Internación Externa » Anulación de Solicitudes de Internación.

Si la misma es autorizada a ingresarse, luego podrá gestionarse su circuito administrativo desde la pantalla principal del admisionista.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000004F3000001E0F956079E6206FA7D.png" style="width:15cm;height:5.681cm" />

#### **6. Seguimiento administrativo de Paciente**

Personal de admisión y personal de facturación realizan el control de la existencia de órdenes de internación y prórrogas.

Si la orden de internación se encuentra vencida o no existe, todos las prestaciones, insumos y medicamentos se encuentran a cargo del paciente con el valor definido para estos casos en el financiador.

Diariamente, mediante el empleo de consultas del sistema de facturación, el personal de admisión verificará el estado de cuenta del paciente y estados de órdenes de internación y prórrogas.

Si los valores de gastos realizados exceden el cubierto por la prepaga y el depósito en garantía, el personal de admisión debe gestionar pagos o depósitos en garantía adicionales en la cuenta del paciente.

El sistema emitirá alarmas sobre los pacientes internados con estado de cuentas deudoras para la revisión y control detallado.

A pedido de familiares o el paciente, admisión puede emitir en cualquier momento un resumen de gastos realizados hasta el momento, los pagos realizados y garantías depositadas.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000756000001BF11CF4C2A279CCFF7.png" style="width:15cm;height:3.572cm" />

#### 3.2.7 Gestión de Camas

El censo gráfico permite visualizar en tiempo real todos los ambientes y camas por sector de internación. Junto con esto, otorga una interfaz gráfica de indicadores/alarmas que se utilizan en la gestión medica/administrativa (alergias, solicitudes de estudios y medicación con sus vigencias, aislamientos, etc).

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000075D00000397CB4C2BB3E0590528.png" style="width:15cm;height:7.313cm" />

Dentro de la gestión administrativa, permite agilizar el proceso de gestión de camas mediante la interacción directa con las distintas ocupaciones.

- **Datos del paciente:** información relevante del paciente (nombre, apellido, tipo y número de documento, convenio, diagnóstico, etc.)
- **Liberar Cama:** desocupación de la cama para dar lugar a su preparación y nueva ocupación. Si ya se ha dado alta médica y administrativa (según la configuración), la liberación de la cama otorga el alta física.
- **Cambiar Cama:** asignación de una nueva cama disponible. El paciente quedará asignado, según el caso, al servicio vinculado de la nueva ocupación.
- **Cambiar función Cama:** asignación de una nueva función a la ocupación, la cual comenzará a regirse por las prestaciones asociadas de la nueva función.
- **Cambiar Estado Ambiente:** asignación de un nuevo estado al ambiente, lo cual afectará a todas las ocupaciones que contiene. Los pacientes involucrados quedaran EN TRANSITO.
- **Cambiar estado Cama:** asignación de un nuevo estado a la cama. Solo puede realizarse si no se encuentra ocupada.

Los estados de los Ambientes pueden ser:

HABILITADA – LIMPIEZA – AISLACION – DESINFECCION – REPARACION

Los estados de las camas pueden ser:

LIBRE – RESERVADA – LIMPIEZA – DESINFECCION – INHABILITADA – REPARACION

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000238000001BA457F2F02D927A575.png" style="width:8.848cm;height:6.884cm" />

Una vez realizada la admisión del paciente sin ocupación asignada, según su tipo de internación, puede estar clasificado de la siguiente manera:

**EN TRANSITO:** paciente que luego de ser admisionado, tuvo movimiento en el histórico de ocupaciones y actualmente se encuentra sin cama, a la espera de una nueva asignación.

**HOSPITALARIA:** paciente de ese tipo internación que nunca tuvo asignación de ocupación.

**AMBULATORIA:** paciente de ese tipo internación que nunca tuvo asignación de ocupación.

**TRANSITORIA:** paciente de ese tipo internación que nunca tuvo asignación de ocupación.

**BEBE EN TRANSITO**: paciente recién nacido que proviene de una reciente desvinculación de la internación conjunta y se encuentra a la espera de una nueva asignación o el alta definitiva.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/1000000000000126000000BCC1A59C3653F99979.png" style="width:5.863cm;height:3.748cm" />

#### **8. Alta médica y administrativa**

El proceso de egreso comienza con el alta médica, la cual es realizada por un médico responsable.

El **alta médica** inhibe la realización de indicaciones ya sea de medicamentos o de estudios. Luego de que el paciente reciba el alta médica se procederá a realizar el Alta Administrativa. El alta médica es informada por mecanismos de referencias a facturación y admisión en forma simultánea para facilitar el cierre administrativo del paciente a la brevedad posible.

El área administrativa se comunicará con la habitación para que algún acompañante del paciente asista a Tesorería/Cajas para completar el trámite. Si el paciente no debe abonar deuda alguna, admisión tiene la potestad de emitir el **alta administrativa**, que acredita que todos los aspectos formales de la internación por parte del paciente están realizados. La tarjeta del alta puede ser emitida por pantalla e impresa.

Si el paciente debe abonar adicionales o conceptos no cubiertos por la prepaga y obra social, el paciente deberá dirigirse a la caja correspondiente a saldar la diferencia de su cuenta corriente. Opcionalmente, la tarjeta del alta será presentada a la enfermera en el office del sector de internación correspondiente para que el paciente se pueda retirar de la cama asignada.

En caso de que un paciente al alta requiera una ambulancia, la misma deberá ser solicitada por el paciente o acompañante al secretario de piso (o responsable administrativo de piso) para que facilite el llenado de formulario de alta, con la indicaciones del médico tratante.

Una vez constatada la desocupación del ambiente, el personal a cargo del paciente procede a realizar el **alta física**, dándole la liberación de la cama en el sistema e iniciando el proceso de preparación de la ocupación para ser asignada nuevamente.

*Tipos de Alta*

Existen distintos tipos de altas, los cuales son parametrizables junto a los destinos y vinculados a un formulario de alta respectivo. La configuración se realiza en Administración General » Dominios Médicos » Tipo Alta.

Ejemplos de Tipo de Alta:

1)  Alta definitiva
2)  Retiro Voluntario
3)  Retiro voluntario no manifiesto
4)  Óbito
5)  Derivación
6)  Domiciliaria

Devolución de saldo

En el caso de que una vez emitida la factura al paciente por todos los gastos a cargo del paciente y realizar su pago, existiera un saldo acreedor en la cuenta corriente del paciente, se producirá la devolución de dicho importe en la caja asignada a admisión.

El sistema de caja debe chequear que la factura del paciente haya sido emitida y se encuentre abonada.

#### **9. Facturación de extras**

El proceso de facturación de este tipo de prestaciones comienza con la carga prevista de las prestaciones contempladas como EXTRAS en la internación de un paciente.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F000001B63282F77D852513F9.png" style="width:15cm;height:3.424cm" />

Una vez previstos, se procede a efectivizar el consumo vigente de cada uno de esos extras, a fin de que luego sean contemplados correctamente en su facturación a paciente o a convenio (cubiertos en la orden de internación).

Se contempla que un sector/servicio definido será el encargado de confirmar la realización de los extras.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000033A000000EC03EBBE11D81EE9B3.png" style="width:9.631cm;height:2.752cm" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F0000039C02501F11EE22C86D.png" style="width:15cm;height:7.221cm" />

Una vez generados, los extras son facturados por la admisión correspondiente, quien emitirá la correspondiente factura a paciente, junto con el traslado del importe cubierto por el financiador al facturador de convenios.

Previo a la emisión de la factura, pueden agregarse prestaciones e ítems adicionales que hayan sido realizadas y no efectivizadas por el curso normal de facturación de extras.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/10000000000001740000008043767AC4E30548BF.png" style="width:7.876cm;height:2.709cm" />

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000077F0000039C9A70F94D19B69688.png" style="width:15cm;height:7.221cm" />

#### 10. Consultas

Existen diversas consultas administrativas que ayudan a gestionar y conocer cómo se desarrollan los procesos dentro del ámbito de internación. Se describirán las más importantes.

Consulta Censo General

La consulta devuelve todas las internaciones realizadas hasta la fecha seleccionada como parámetro. En la vista se puede observar el total de internados por sector de internación, las altas previstas, las altas correspondientes al día de la búsqueda y a las altas del día anterior así como los Óbitos de ambos días. Se puede exportar la información o emitir un reporte impreso.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000076F000003623FAFCC39A3F46BA2.png" style="width:14.288cm;height:6.502cm" />

Consulta Generador Censo

La consulta devuelve todas las internaciones realizadas hasta la fecha seleccionada como parámetro con la posibilidad de agregar filtros para realizar la búsqueda y ordenadores para su visualización. Se puede exportar la información o emitir un reporte impreso.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000076F00000357832E6477DA30D8C8.png" style="width:15cm;height:6.74cm" />

Altas entre fechas

Se visualizan las altas realizadas entre las fechas seleccionadas con la posibilidad de agregar filtros para realizar la búsqueda.

Ingreso entre fechas

Se visualizan los ingresos realizados entre las fechas seleccionadas con la posibilidad de agregar filtros para realizar la búsqueda.

Libro de internación

Se visualizan todos los ingresos mensuales de internaciones, con posibilidad de utilizar los ordenadores de columnas e imprimir el reporte si fuera necesario.

<img src="/mnt/data/.tmp_conversion/Markdown_Convertidos/.media_tmp/Pictures/100000000000076E0000036501BCBDBA5D17F6A8.png" style="width:15cm;height:6.853cm" />
