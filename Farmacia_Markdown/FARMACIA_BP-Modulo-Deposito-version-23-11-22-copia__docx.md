Fuente original: BP Módulo Depósito versión 23-11-22 - copia.docx
Ruta original: Farmacia/BP Módulo Depósito versión 23-11-22 - copia.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image1.png" style="width:2.94792in;height:0.5in" />

**BP – Modelo de Negocios**

**Depósito**

**Versión 1.0**

# * Información del Documento*

|  | **Título del Documento** | BP – Modelo de Negocios – Depósito |
|:---|:---|:---|
| **Información General** | **Localización del documento** |  |

| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|:---|:---|:---|:---|:---|
|  | Ing. Nazarena Gareis | Soporte funcional | Analista Funcional | 20/10/22 |
|  | Ing. Silvana Elizondo | Coordinación interna | Líder de Proyecto | 20/10/22 |

| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|:-----------------|:---------------------|:-------------|:--------|:----------|
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

#### Contenido

[*Información del Documento* 2](#información-del-documento)

[Contenido 4](#contenido)

[1. Objetivos 6](#objetivos)

[2. Alcance del Proyecto 7](#alcance-del-proyecto)

[1 Parámetros Generales 8](#parámetros-generales)

[1.1 Tipo de movimientos del inventario 8](#tipo-de-movimientos-del-inventario)

[1.2 Tipo de Inventario 10](#tipo-de-inventario)

[1.3 Tipo de Ítems 11](#tipo-de-ítems)

[1.4 Habilitación de Ítems 15](#habilitación-de-ítems)

[2. Pedidos Depósito 18](#pedidos-depósito)

[2.1 Pedidos desde Servicios 21](#pedidos-desde-servicios)

[2.2 Preparación y Entrega de Pedidos Deposito/servicio/área 23](#preparación-y-entrega-de-pedidos-depositoservicioárea)

[2.4 Trasferencia directa depósito 23](#trasferencia-directa-depósito)

[3. Trazabilidad 23](#trazabilidad)

[4. Fraccionamiento 26](#fraccionamiento)

[5. Circuito pacientes internados 28](#circuito-pacientes-internados)

[5.1 Preparación y Entrega Pacientes Internados 30](#preparación-y-entrega-pacientes-internados)

[5.2 Devolución paciente internado 32](#devolución-paciente-internado)

[5.3 Dispensación Directas a Pacientes 33](#dispensación-directas-a-pacientes)

[6. Pedidos de cirugía 33](#pedidos-de-cirugía)

[7. Ajustes 35](#ajustes)

[8. Control de Inventario 35](#control-de-inventario)

[9. Consultas 35](#consultas)

[STOCK 35](#stock)

[CONSULTA A.N.M.A.T 36](#consulta-a.n.m.a.t)

[ÍTEM TRAZABLE 37](#ítem-trazable)

[MAESTRO FARMACIA 37](#maestro-farmacia)

[RECEPCIÓN COMPRA 38](#recepción-compra)

[PRECIO VENTA ITEM 38](#precio-venta-item)

[CONSULTA NECESIDAD DE COMPRA 38](#consulta-necesidad-de-compra)

[PROVISIÓN EXTERNA 38](#provisión-externa)

[ÍTEMS EN CONSIGNACIÓN 39](#ítems-en-consignación)

[PEDIDOS 39](#pedidos)

##### 1. Objetivos

En el siguiente documento se describirán los procesos asociados a los depósitos, contemplados en el sistema Thinksoft HIS.

Es importante aclarar que al igual que el módulo de Depósito está dividido en procesos detallados que quedan descritos mediante su correspondiente diagrama de negocios (BPMN) en este documento.

**Objetivo del documento “BP – Procesos de negocio”:**

El presente documento tiene como objetivo principal documentar y *aprobar* todos y cada uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con otros sistemas informáticos implementados en la institución.

La correcta documentación de estos procesos está sujeta a las reuniones continuas con los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir, así como las oportunidades de cambio y mejora. Cada proceso plasmado en este documento debe ser aprobado por el referente designado, y todo aquello que no se registre adecuadamente en esta instancia puede suponer una demora en los tiempos de implementación a futuro.

##### 2. Alcance del Proyecto

El presente documento tiene como finalidad explicar todos los procesos y parametrización correspondiente a la farmacia y depósitos correspondientes.

En el alcance del proyecto se deberá definir el Inventario (producto comercial o genérico), los personales, movimientos y habilitaciones vinculados a cada depósito.

Se dejarán descriptas también de forma general, todas las consultas de gestión disponibles para todos los procesos que intervienen.

# 1 Parámetros Generales

- Cada Sucursal Empresa definida en el sistema tiene un único inventario.

- El inventario de materiales, equipos y suministros está compuesto por todos los ítems que se encuentran en los depósitos que pertenecen a la Empresa.

- Cada depósito está asociado a una empresa y es independiente de los centros de atención y centro de compras. Esto significa que un depósito puede proveer ítems a varios centros o generar requisiciones de compras a varios centros de compra (un centro de compra puede ser de medicamentos y descartables, otro de equipos e instrumentales, etc.).

# 1.1 Tipo de movimientos del inventario

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image2.png" style="width:5.76806in;height:3.99583in" />

Existen dos grandes grupos de tipo de movimiento:

- <u>Movimientos **internos** del inventario.</u> No se producen variaciones cuantitativas en el total del inventario. Solo se producen movimientos físicos de ítems dentro del inventario.

  - **Transferencia entre depósitos del mismo inventario.** Puede ser con pedido o sin pedido.

- <u>Movimientos **externos** del inventario.</u> Cada movimiento produce variaciones cuantitativas en el total del inventario. Cada uno de estos movimientos puede ser de ingreso o egreso.

  - **Movimientos a pacientes**

    - Indicación

    - Dispensación

    - Administración

    - Devolución de reserva de paciente

  - **Movimientos a proveedores**

    - Proceso de compras

<!-- -->

- Ingreso

- Devolución de proveedores

  - Consignaciones

<!-- -->

- Ingreso

- Devolución de consignación

  - **Movimientos a financiadores**

    - Provisión

    - Devolución de provisión

  - **Movimientos a Instituciones**

    - Préstamos

    - Devolución de prestamos

  - **Movimientos a Servicios/áreas organizacionales**

    - Dispensación a servios/áreas organizacionales

    - Devolución a servios/áreas organizacionales

  - **Ajustes**

    - Control de inventario

    - Rotura

    - Pérdida

    - Otros ajustes definibles por el usuario

<u>Estado de los ítems en el depósito</u>

Los ítems dentro del inventario, pueden estar disponibles, en tránsito o en reserva. Dentro del stock disponible, se encuentra el propio, los préstamos y las consignaciones. En la cantidad en tránsito, tenemos a los ítems en cuarentena, que son aquellos no validados por anmat y los ítems pendientes de confirmar (se realizó una transferencia entre depósitos y el depósito no confirmó su recepción). Los ítems reservados son los que se encuentran en la reserva ambulatoria (reservado en la farmacia para vender a los pacientes ambulatorios), reserva internado (reserva que se envía al office correspondiente para cumplimentar la medicación pedida por 24hs), la preparación del pedido y la provisión externa.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image3.png" style="width:2.64931in;height:2.74097in" />

# 1.2 Tipo de Inventario

La cantidad de depósitos y su forma de interactuar es configurable por el usuario.

Por cada depósito se definen los tipos de movimientos habilitados de ingreso y egreso. De esta manera se pueden configurar esquemas de depósito en árbol o en grafos.

Para cada depósito se define el personal habilitado a su acceso y el horario permitido. Asimismo, en él se deben habilitar los genéricos o productos comerciales que manejará y administrará.

**CLASE DE ITEMS GESTIONADOS**

Cada depósito puede gestionar y administrar los ítems por las siguientes formas:

1.  Producto comercial

2.  Ítem genérico

3.  Fraccionado

4.  Multidosis

5.  Kit

6.  Ítem compuesto

7.  Auto-calculable

# 1.3 Tipo de Ítems

> El sistema de stock permite la registración, gestión, administración de diferentes ítems provenientes de grupos distintos.
>
> Los tipos de ítems, se clasifican en:

1.  Ítems farmacológicos (medicamentos)

2.  Insumos y material descartable

3.  Ítem compuesto

4.  Equipo (electrobisturí, monitor multiparamétrico, etc)

5.  Instrumental (forceps, tijeras, etc.)

6.  Ítems varios (ej. ropa, sábanas, etc.)

7.  KIT

Cada grupo a su vez se puede clasificar en diferentes sub-tipos, definibles y parametrizables por el usuario.

**Ítems Farmacológicos e Insumos y Materiales Descartables**

El ítem es un elemento de existencia física en un depósito y representa a cada producto que se tiene en el stock. Los ítems pueden ser comerciales o genéricos.

Para descartables o aquellos productos comerciales que no estén en alfabeta, o bien en caso que no usen alfabeta, se pueden agregar.

Un Ítem Comercial es un producto comercial compuesto por:

***Ítem Comercial = Producto Comercial + Presentación + Laboratorio***

Cuando la diferencia de valor es despreciable entre diferentes productos y tampoco es deseable conocer de qué laboratorio o marca comercial proviene el ítem, se puede utilizar los ítems genéricos, cuya definición es:

***Ítem Genérico = Genérico + Presentación***

Genérico = Droga + Potencia + Unidad Potencia + Forma farmacéutica

Un Ítem genérico posee un laboratorio genérico.

En un depósito pueden coexistir ítems comerciales e ítems genéricos, aun si pertenecen al mismo genérico.

Previo la creación del ítem, se debe contar con los siguientes puntos definidos:

- Sub-Tipo de ítem

- Laboratorio fabricante y producto comercial

- Vías de administración

- Forma Farmacéutica

- Unidad de potencia

- Tamaño Ítem Farmacológico

- Monodroga

La siguiente información es necesaria para definir un ítem

<table>
<colgroup>
<col style="width: 30%" />
<col style="width: 69%" />
</colgroup>
<thead>
<tr>
<th style="text-align: left;"><strong>Código Ítem</strong></th>
<th style="text-align: left;"><p>OBLIGATORIO.</p>
<p>Código único de identificación de un ítem. Uso interno, en general el usuario final no utiliza este código</p>
<p>Alfanumérico de 12 caracteres</p></th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: left;"><strong>Laboratorio</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO.</p>
<p>En ítem genéricos su valor se prestablece a GENERICO</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Producto</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO.</p>
<p>Ítem comerciales: nombre del producto comercial</p>
<p>Ítem genérico: nombre del genérico</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Presentación</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Genérico</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Droga</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Potencia</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Unidad de potencia</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Forma farmacéutica</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Vía de administración</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Ctd fraccionable</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO.</p>
<p>Ctd. de ítem indivisibles que componen el ítem</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Venta libre</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO.</p>
<p>Determina si el ítem de venta libre, sin necesidad de prescripción</p>
<p>El valor se establece en S o N</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Control del ítem</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO</p>
<p>Define la forma de controlar un ítem al momento de prescribir y entregar. Los valores posibles son:</p>
<p>NO_CONTROLADO</p>
<p>PSICOTROPICO II</p>
<p>PSICOTROPICO III</p>
<p>PSICOTROPICO IV</p>
<p>ESTUPEFACIENTE I</p>
<p>ESTUPEFACIENTE II</p>
<p>ESTUPEFACIENTE III</p>
<p>VENTA VIGILADA</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Trazable</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO.</p>
<p>Establece si el ítem será trazable de alguna de las formas disponibles por el sistema</p>
<p>El valor se establece en S o N</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Tipo trazable</strong></td>
<td style="text-align: left;"><p>OPCIONAL</p>
<p>Define el tipo de trazabilidad a utilizar en el Ítem si este es trazable. La trazabilidad puede ser definida por entes externos (ej. Anmat) o por controles internos (ej. Fecha de vencimiento)</p>
<p>Los valores posibles son:</p>
<p>ANMAT MEDICAMENTO</p>
<p>ANMAT PRODUCTO</p>
<p>SOLO FECHA DE VENCIMIENTO</p>
<p>SOLO NRO LOTE</p>
<p>SOLO NRO SERIE</p>
<p>FECHA DE VENCIMIENTO Y NRO DE LOTE</p>
<p>FECHA DE VENCIMIENTO Y NRO DE SERIE</p>
<p>NRO SERIE Y NRO LOTE</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>GTIN</strong></td>
<td style="text-align: left;">Global trade item number , Código comercial del producto según el estándar GS1</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Requiere refrigeración</strong></td>
<td style="text-align: left;"><p>OBLIGATORIO.</p>
<p>Establece si es necesario refrigerar al ítem tanto en su almacenamiento como en el transporte</p></td>
</tr>
<tr>
<td style="text-align: left;"><p><strong>Código Alfabeta</strong></p>
<p><strong>(base de precio)</strong></p></td>
<td style="text-align: left;"><p>OPCIONAL</p>
<p>Define el ítem de alfabeta que será utilizado para actualizar el precio de venta.</p></td>
</tr>
<tr>
<td style="text-align: left;"><strong>Exento IVA</strong></td>
<td style="text-align: left;">OBLIGATORIO.</td>
</tr>
<tr>
<td style="text-align: left;"><strong>Código ATC</strong></td>
<td style="text-align: left;">OPCIONAL</td>
</tr>
</tbody>
</table>

<u>Multidosis</u>

Un ítem definido como multidosis permite al usuario extraer una cantidad apropiada del producto (dosis) en más de una ocasión.

El ítem se presume que tiene un número determinado de dosis, pero no hay seguridad de ello. Por ejemplo, un gotero puede tener aproximadamente 1000 gotas, pero no hay seguridad que sean 1000, 1100 o 950. Por tal motivo la reposición del ítem de multidosis se realiza cuando supera un umbral de dosis definible para su pedido. Ej. se define que el ítem A tendrá una reposición (pedido y entrega de farmacia) cuando se haya consumido una determinada cantidad de dosis.

**Genérico Equivalente**

El genérico es una clasificación de ítems. Un ítem puede pertenecer a un único genérico.

La definición de Thinksoft de GENERICO es más específica que la habitual, y consideramos a:

***Genérico = Droga + Potencia + Unidad Potencia + Forma farmacéutica***

Cualquier variación de potencia o unidad de potencia o forma farmacéutica para la misma monodroga son un genérico diferente.

Una vez creado el genérico, podemos asociar los ítems a dicho genérico.

**Conversión Ítem Genérico**

Se puede utilizar la conversión a Ítem genérico para poder transformar cualquier marca comercial comprada al ítem genérico creado en las recepciones de compras, generando el movimiento de fraccionamiento por el ingreso de la marca al ítem elegido.

Luego se debe buscar el ítem al cuál se convertirá dicho genérico (sólo puede ser un ítem del mismo genérico cuya cantidad fraccionable sea la mínima unidad, es decir 1)

**Equipos e Instrumental**

Los equipos e instrumental pueden agruparse por tipo y/o uso. Estos son configurables por el usuario.

**Ítems Varios**

Estos ítems engloban todos aquellos que no pertenecen a las categorías definidas anteriormente, por ejemplo: sábanas, bolsas, baterías, etc.

**Kit**

Cada KIT tiene código propio, tiene movimiento de stock.

Al momento de armar el kit, se genera el movimiento de egreso de stock de los ítems que lo componen y un ingreso en el stock del KIT.  
Cuando se entrega el KIT, el movimiento de egreso es para el KIT y no para los ítems que lo componen. Esto agiliza y separa el momento de armado y entrega.  
En caso de devolución, se hace por ítems.

En un primer paso se deben definir los tipos de KITs, en donde se especifican los detalles (ítems) generales que lo conforman.

Luego se procede al armado del KIT propiamente dicho, en donde se pueden modificar los ítems y cantidades de éstos, pre definidos en el tipo de kit.

**Set**

Los SET están asociados a **prestaciones**.

No poseen código y stock propio. En el momento en que se realiza la práctica, se descargan los ítems utilizados, definidos en el set, generando un ingreso y egreso en el stock del ítem.

**Habilitaciones en depósitos**

# 1.4 Habilitación de Ítems

Para la posterior habilitación de los ítems farmacológicos en farmacia, es necesario contar con los genéricos habilitados que están asociados a éstos en la empresa.

Cuando se habilita un genérico en la empresa, se debe seleccionar una fecha de control (fecha de alta en la empresa), la habilitación de drogas <img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image4.png" style="width:3.09722in;height:4.32639in" />(ésta puede realizarse en cualquier momento) y se da la opción de definir aquellos que requieran un formulario de autorización.

**Habilitación de drogas**

Por Centro de Atención se definen las drogas habilitadas y las habilitadas que requieren autorización.

El médico no podrá prescribir drogas no habilitadas en el centro de atención.

Al prescribir una droga Habilitada, pero que requiere Autorización, el profesional junto con la prescripción debe completar un formulario de Autorización de Drogas. La indicación queda en estado pendiente.

**Habilitación de Ítems en Depósito**

Lo ítems pueden habilitarse de dos maneras por:

- Genérico.

- Item.

Para ingresar a estas opciones, puede accederse desde el módulo de Administración General o bien desde Depósito.

**Por Genérico**

Cuando se habilita un genérico en un depósito, se están habilitando todos los ítems que éste tiene asociado de forma automática.

La marca “*Dispensa por Genérico*”, hace referencia a los casos cuando la diferencia de valor es despreciable entre diferentes productos comerciales y tampoco es deseable conocer de qué laboratorio o marca comercial proviene el ítem.

En caso que se desee excluir un producto comercial asociado al genérico habilitado anteriormente, el sistema da esta posibilidad ingresando a la opción *Exclusión por Depósito de Producto Comercial*.

También puede usarse para casos en que se desee excluir un producto comercial que ya no trabajará el depósito.

**Por Ítem**

Cuando no se desea habilitar todos los productos comerciales, en lugar de habilitar por genérico, se utiliza la opción de habilitar por ítem o bien cuando se define que el depósito trabajará solo con un producto comercial de ese genérico.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image5.png" style="width:5.76806in;height:4.29722in" />

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image6.png" style="width:5.76806in;height:4.21181in" />

**Ítems en Depósito**

Cada vez que se habilita un genérico o un producto comercial, los ítems correspondientes se muestran en la opción Ítems en Depósito, en donde se da la opción de configurarles si afecta o no al stock.

*En un depósito pueden coexistir ítems comerciales e ítems genéricos, aun si pertenecen al mismo genérico.*

Se permite definir ubicación del ítem por cada depósito. Las ubicaciones son definibles por el usuario.

**Afectación de stock**

En el sistema de control y gestión de ítems, no se permite el stock negativo. Para paliar la funcionalidad a veces necesaria del stock negativo se permite para cada ítem por depósito definir si afecta stock o no.

Si un ítem no afecta al stock, su cantidad en existencia en el stock es cero y no se valoriza en el inventario.

El mismo ítem en diferentes depósitos puede afectar o no el stock. Por ejemplo, se puede definir que en la farmacia central el algodón y las gasas afectan el stock y, en consecuencia, cualquier movimiento de ingreso o egreso a dicho depósito afectará al stock. En un depósito periférico de enfermería se puede definir que el algodón no afecte el stock.

Si un ítem no afecta stock en un depósito entonces ningún tipo de movimiento que se efectúe sobre el mismo afectará al stock, es decir, si se realizan transferencias o entregas a pacientes, o cualquier tipo de ingreso o egreso que se realice con ese ítem en ese depósito no moverá stock.

# 2. Pedidos Depósito

Existe la posibilidad de generar pedidos a un depósito por transferencia.

**Pedido Automático**

La generación automática de pedidos, parte de la definición de políticas de reposición.

Existen tres políticas de reposición:

- Reposición de consumo: se genera el pedido de la cantidad consumida desde el último periodo. Ej: se define el nivel de referencia de stock que debe tener el ítem y el pedido se realiza para completar el stock actual al nivel de referencia.

- Lote Bajo Mínimo Cantidad Constante: cuando el nivel de stock decae por debajo de un mínimo predefinido, se pide un lote previamente definido.

- Lote Bajo Mínimo Tiempo Constante: al momento de llegar al periodo de revisión (periodo de pedido fijo), se realiza un pedido teniendo en cuenta el stock actual, el punto mínimo de pedido y la cantidad de referencia de stock del ítem.

Se chequea qué ítems habilitados en el depósito tienen configurado una política de reposición. Dependiendo de ésta, se genera un pedido por alcanzar el nivel de stock mínimo (por consumo) o la cantidad definida del lote de reposición por cada ítem (por lote).

Cuando se define la política de reposición, se genera automáticamente el pedido y se puede configurar la confirmación manual del pedido automático.

Al realizar la confirmación manual, los pedidos se listan permitiendo modificar la cantidad a pedir.

Las políticas de reposición se configuran por depósito por ítem.

**Pedido Manual**

Para la creación de pedidos manuales se debe seleccionar el depósito al cual se realizará el pedido.

La carga de ítems, puede realizarse de forma individual, seleccionando ítem por ítem, o masivamente, importando un archivo con el código de ítem y la cantidad necesaria.

La selección de ítems puede realizarse por varias clasificaciones, por ejemplo: genérico, producto comercial, acción farmacológica, rubro y sub-rubro, tipo de ítem.

Es posible definir una prioridad por ítem en cada pedido.

Una vez generado el pedido, se debe confirmar para enviar el pedido manual al depósito.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image7.png" style="width:5.76806in;height:3.42917in" />

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image8.png" style="width:9.19306in;height:7.40694in" />

# 2.1 Pedidos desde Servicios

Desde recepción, se pueden generar pedidos manuales a servicios, eligiendo el depósito, los ítems (los cuales pueden ser productos comerciales o genéricos) y su cantidad. Si los ítems se encuentran en stock en el depósito, se prepara y entrega el pedido. Caso contrario, desde el depósito, se debe generar un pedido al centro de compras. Para que el mismo pueda generar el pedido, debe estar habilitado para realizar pedidos al centro de compras.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image9.png" style="width:9.19306in;height:5.66528in" />

# 2.2 Preparación y Entrega de Pedidos Deposito/servicio/área

En la preparación, se listan los pedidos pendientes de las áreas organizacionales, servicios y depósitos a ser preparados. Allí se cargan los ítems y las cantidades según los pedidos ingresados. También es posible observar los códigos, cantidad en stock y cantidad a preparar.

Por otra parte, en la entrega, es posible realizar una modificación de las cantidades preparadas anteriormente. Una vez aceptada la entrega se realiza el movimiento de transferencia.

# 2.3 Trasferencia directa depósito

Existe la posibilidad de transferir sin generar un pedido desde el depósito. Se controla que los ítems estén habilitados en el depósito a transferir. En caso que el depósito requiera confirmación en la entrega, se debe realizar la misma para recepcionar los ítems transferidos.

# 3. Trazabilidad 

Como mencionamos anteriormente, en la configuración de un ítem, comercial o genérico, se debe establecer si el mismo es trazable. Si el ítem es definido como trazable, es necesario configurar el tipo de trazabilidad, el cual puede estar definido por entes externos, como el anmat, o por controles internos. La fecha de inicio de la trazabilidad del ítem es configurable en el mismo.

Si el ítem es definido como trazable y con tipo de trazabilidad dado por anmat, se debe contar con el GTIN, el número de lote, el número de serie y la fecha de vencimiento al momento de recepcionar el ítem. El sistema consulta los datos con anmat, si los mismos son validados el ítem ingresa al stock en disponible, caso contrario se ingresa como ítem en cuarentena hasta que no se corrijan los datos necesarios para que el mismo se valide. El momento en el que se debe informar a anmat es configurable y puede ser en fin de empaque o a pie de cama. Dicha configuración se realiza en el genérico equivalente, el cual debe estar asociado al ítem.

Si, en cambio, el ítem es definido como trazable y con tipo de trazabilidad dado por controles internos, se verifica si es según las fechas de vencimiento, número de lote o número de serie o variaciones de combinaciones de estas últimas tres, por lo que el usuario debe completar dicho campo al recepcionar el ítem, ya que se solicita como obligatorio.

Existe la posibilidad, al realizar una recepción, de seleccionar si el ítem es un trazable no trazado y de esta manera no se le requiere al usuario realizar la carga de los campos obligatorios que el sistema solicita según el tipo de trazabilidad.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image10.png" style="width:4.24792in;height:2.39236in" />

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image11.png" style="width:9.19306in;height:2.47361in" />

# 4. Fraccionamiento

**Definición de Fraccionado**

Un ítem fraccionable se define como aquel que se encuentra configurado con una cantidad fraccionable mayor a uno. Para que el mismo se pueda fraccionar, se debe definir la codificación del ítem fraccionado.

Una vez generado la definición del nuevo ítem (fraccionado), el ítem farmacológico queda conformada por:

***FRACCIONADO** = Prod. Comercial + Potencia + Unidad de potencia + Forma Farmacéutica + Presentación (X 1)*

**Fraccionamiento del Ítem**

Para realizar el fraccionamiento de un ítem, el mismo debe poseer la definición de su fraccionado.

Existen dos modalidades de fraccionamiento. Las mismas pueden ser fraccionamiento manual o fraccionamiento automático, lo cual es configurable por depósito.

Si el fraccionamiento es manual, se debe seleccionar la presentación comercial del ítem a fraccionar y la cantidad que se va a fraccionar. En este proceso se debe contar con stock del ítem en el depósito. En cambio, si el fraccionamiento es automático, al realizar la recepción de los ítems, los mismos son fraccionados automáticamente.

Al realizar el proceso de fraccionamiento, se generan movimientos de stock internos en el depósito, dados por un egreso en el ítem a fraccionar y un ingreso en el nuevo ítem fraccionado.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image12.png" style="width:9.19306in;height:3.40556in" />

# 5. Circuito pacientes internados

Desde internación, el médico realiza una indicación al paciente, la cual puede ser mediante una indicación preferencial, monodroga o genérico equivalente. Las indicaciones preferenciales son indicaciones configurables para que el médico pueda precargar las mismas. Se les podrá definir, previamente, la monodroga, la potencia y unidad de potencia, la vía de administración, la cantidad, la frecuencia y el genérico equivalente. Además, a la misma se le puede agregar un ítem que será el cual traduzca cuando se realice el pedido a farmacia. Las indicaciones preferenciales se configuran por servicio centro.

Una vez confirmada la indicación, el pedido se genera automáticamente y llega al depósito al cuál se configuró desde el sector de internación para realizar el pedido. Allí, el farmacéutico prepara y entrega el pedido, seleccionando la cantidad y los ítems a dispensar. El sistema ofrece la posibilidad de realizar las preparaciones y las entregas en un solo paso.

Existe la posibilidad de que el médico seleccione en la indicación que la medicación será provista por el paciente, lo cual no genera el pedido al depósito.

Al confirmar la entrega del pedido, el mismo se recibe en el office, al cuál está asociado el sector de internación, como reservado al paciente. Esto es lo que se denomina como reserva paciente internado. La enfermera puede administrar los ítems dispensados y también si se encuentra en stock en el depósito asociado. Posteriormente, se deberá realizar la devolución de los ítems sobrantes, la cual explicaremos más adelante.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image13.png" style="width:5.76806in;height:3.96389in" />

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image14.png" style="width:5.76806in;height:4.07083in" />

# 5.1 Preparación y Entrega Pacientes Internados

**Preparación Pedidos Internados**

En el depósito, en la preparación de pedidos internados, se observan los pedidos de medicamentos realizados mediante una indicación médica o pedido manual realizado por enfermería.

Los mismos se pueden clasificar por sector, servicio y/o paciente. Además es posible filtrar por prioridad ya que el sistema contempla si la prescripción requiere urgencia en la entrega (pedido en rojo). Este es el caso en que el médico realiza una indicación con prioridad Inmediata.

En el pedido se observa la cama del paciente, nombre y apellido, convenio, servicio, alertas (por ejemplo alergias), personal que realizó el pedido, fecha, cantidad del pedido, cantidad pendiente a preparar, pendiente a entregar, sin confirmar o con novedad. Como parte de las acciones que puede realizar el farmacéutico o técnico sobre el pedido se incluye la preparación propiamente dicha, impresión de indicaciones, impresión de pedidos o eliminación del mismo.

Cuando se procede a preparar el pedido de medicamentos, el sistema puede o no realizar traducciones de las indicaciones:

- Si la indicación se realiza a través de una preferencial y la misma tiene configurada la conversión del pedido, el sistema no traduce ni convierte la indicación sino que se pide lo que se configuró.

- Indicación por preferencial sin configuración de pedido: el sistema traduce según la conversión que se explica más abajo.

- Indicación sin preferencial: el sistema traduce según la conversión.

**Traducción de Indicaciones**

Primero se busca misma unidad de potencia de un ítem habilitado que sea fraccionado (es decir cantidad fraccionable igual a 1), de no existir se continúa buscando por fracción de dosis:

- ½

- ¼

- ⅒

- OTROS

Si se encuentra un ítem se pide la cantidad de ítems necesarios para cumplimentar cada una de las dosis a administrar. En el caso de que la cantidad de ítems pedidos multiplicado por la potencia sea igual a la indicación el estado de la traducción es *TRADUCCIÓN EXACTA*.

En caso de no encontrar ningún ítem con dicha condición se busca con otra unidad menor a la del pedido. Si no hay coincidencias con una unidad menor entonces se busca por unidad mayor y se pide 1 ítem para cada administración. La traducción tendrá estado *TRADUCCIÓN CON SOBRANTE*.

Si no se encuentra ninguna equivalencia de las anteriores, se busca algún producto que tenga la misma monodroga y vía. En este caso el estado del pedido será SIN TRADUCCIÓN.

De igual forma el sistema puede sugerir la medicación a preparar en caso de encontrar coincidencia con la indicación y los ítems con stock en el depósito (nada tiene que ver con la traducción).

El caso particular en que se indica por genérico y éste tiene configurado que dispensa por genérico, el sistema realiza la sugerencia siempre que tenga stock el depósito cualquiera de los ítems asociados al genérico.

En caso que se indique por genérico que no dispensa por, debe ser el personal de farmacia el encargado de seleccionar el ítem a entregar y cantidad.

Una vez terminada la preparación de la medicación solicitada, se confirma, y luego se imprime un ticket con los datos del paciente y la medicación que se va a entregar a piso.

**Entrega Pedidos Internados**

Una vez que el personal de farmacia prepara físicamente toda la medicación para cada paciente, con su ticket correspondiente, deberá chequear la preparación.

El detalle del pedido y de la entrega a realizar se visualizan mediante el detalle de la preparación. Es aquí donde el personal de Farmacia, corrobora la medicación físicamente preparada con el ticket impreso de la preparación.

En caso de ser correcto, se acepta y se realiza la entrega al depósito destino.

Caso contrario, el personal de farmacia podrá volver atrás la preparación. El pedido vuelve al primer estado de preparación de forma automática.

Puede darse el caso en que las indicaciones no están vigentes y el pedido quedó pendiente de entregar.

En el detalle de la entrega se genera un alerta y en el del pedido nos especifica el estado de la indicación (si no está vigente), en este caso se puede eliminar detalle por detalle o bien volver atrás la preparación, pero en este caso no queda el pedido pendiente de preparar, sino que se anulan todos los detalles.

El sistema permite la posibilidad de parametrizar la preparación y entrega en un solo paso de confirmación. Esto es definible para cada depósito.

# 5.2 Devolución paciente internado

El proceso de devolución se realiza desde el Office hacia el depósito de la farmacia central. En la devolución de paciente internado se visualiza un listado de ítems farmacológicos que fueron reservados al paciente inicialmente (movimiento de transferencia entre depósitos) pero que no se utilizaron en su totalidad. A partir de este listado, es posible seleccionar aquellos ítems que desean devolverse, visualizando la cantidad entregada, reservada y dando la posibilidad al usuario de ingresar la cantidad que se desea devolver.

La forma de registrar consumo se configura por sector de internación y puede ser por planilla de enfermería o por devolución. Según cómo se tenga configurado el registro del consumo se realizarán los cálculos correspondientes. Esto es, si se registra el consumo por planilla de enfermería se genera un movimiento de transferencia y, en caso que quede medicación pendiente de devolver se puede realizar posteriormente dicha devolución. En caso de registrarse el consumo por devolución, se calcula que todo lo no devuelto, fue consumido por el paciente, continuando el circuito a través de facturación de internados.

Por otro lado, si se requiere confirmación en la recepción por ejemplo de Farmacia central, no se efectiviza la devolución hasta que dicha confirmación no se haya realizado. En caso de que se confirme la recepción con una cantidad menor a la devuelta, se deberá realizar un ajuste dando de baja los ítems faltantes.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image15.png" style="width:5.76806in;height:3.61111in" />

# 5.3 Dispensación Directas a Pacientes

Desde el módulo de depósito, se pueden enviar ítems a un paciente internado, los cuales serán administrados de forma automática, sin la intervención de enfermería.

# 6. Pedidos de cirugía

El sistema ofrece la posibilidad de, al reservar una cirugía, realizar un pedido de ítems reservado a paciente con la misma. El depósito al cual se realizan los pedidos de insumo desde la reserva de cirugía, se configura por servicio.

El pedido de ítems se realiza por producto comercial, por genérico, por kit o formulario de pedido. Desde la farmacia asociada se prepara y envía la reserva a paciente al depósito principal del quirófano.

Desde el centro de procedimientos se configuran los depósitos de los cuales se descargan los ítems del parte de insumos dentro de las cirugías. Además, en el parte se pueden descargar los insumos que fueron enviados reservados a pacientes. La entrega y devolución de estos insumos se realizan desde el módulo de centro de procedimientos, desde un apartado que maneja el depósito principal del servicio. Si se realiza una entrega al parte de insumos y no se devuelven ítems, se dan los mismos por consumidos por el paciente. Si se requiere que ítems entregados al parte de insumos deban volver al stock del depósito principal del quirófano, se debe realizar la devolución de los mismo. Además, se pueden descargar al parte de insumos ítems que se encuentran en los depósitos secundarios. Los ítems que fueron enviados a reservada de paciente y no fueron descargados en el parte, también se deben devolver al depósito central.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image16.png" style="width:5.76806in;height:4.11389in" />

# 7. Ajustes

El sistema ofrece la posibilidad de realizar ajustes por ítems individuales. Allí se establece si el movimiento es un ingreso o egreso y se debe indicar el sub-tipo de movimiento, el cual puede ser, por ejemplo: control de inventario, ingreso por proveedores, etc. Los sub-tipo de movimientos son configurables por el usuario.

# 

# 8. Control de Inventario

El control de inventario se puede realizar en línea o en diferido.

El control de inventario en línea, le permite al usuario visualizar el stock en tiempo real y colocar la cantidad controlada. En este control, el sistema realiza el cálculo automático de la cantidad a ajustar.

El control de inventario en diferido, ofrece la posibilidad de filtrar por la fecha en la que se realizó el control. Al aplicar dicho filtro de búsqueda, se muestra el stock que se encontraba en ese momento, permitiendo al usuario colocar la cantidad controlada. En este control, de igual manera que el control de inventario en línea, el sistema realiza el cálculo automático de la cantidad a ajustar. También, se ofrece la posibilidad de cargar un excel con los datos del stock de los ítems a ajustar.

# 9. Consultas

Se pueden realizar consultas del Depósito principal con relación al stock, farmacia, compras, necesidades de compras, entre otras. Serán descritas a continuación.

## STOCK

### Consulta de movimientos de stock

Este tipo de consulta permite al usuario visualizar los movimientos de stock realizados desde el depósito donde el usuario se encuentra logueado. Se puede filtrar por:

1)  Tipo de movimiento

2)  Subtipo de movimiento

3)  Periodo de fecha

4)  Producto comercial

5)  Tipo de ítem o sub tipo de ítem

6)  Si el ítem es de alto costo o de consumo variable

Se contemplan además, las cantidades de ingreso y egreso por producto, el origen y destino del movimiento de stock, la descripción del ítem y genérico.

### Consulta Mov. Stock por tipo de Mov.

Permite observar los movimientos de los ítems que se realizaron, agrupados por tipo de movimientos. A su vez este tipo de consulta requiere que se ingresen los siguientes filtros:

1)  Tipo de movimiento y subtipo de movimiento

2)  Egreso/ingreso

3)  Periodo de fecha

### Consulta Movimientos de Stock agrupados

A diferencia del mencionado anteriormente, proporciona información sobre todos los subtipos de movimientos de stock para poder visualizarlos en una consulta incluyendo las transferencias de ingreso y egreso.

### Stock depósito

Esta consulta permite al usuario conocer la cantidad en stock en el depósito en el que se encuentra logueado. Se podrá filtrar la búsqueda para realizar la consulta:

1)  Por tipo de ítem o sub tipo de ítem

2)  Si el ítem es de alto costo o consumo variable

Al realizar la consulta podrá obtener datos de relevancia relacionados al ítem como: cantidad disponible del ítem, reservada en internado y cirugía, en consignación, de provisión externa, en préstamo y tránsito.

### Stock Item

La consulta stock item permite al igual que la anterior revisar los stocks de medicamentos pero en todos los depósitos. Podrá filtrar por:

3)  Tipo de ítem o sub tipo de ítem

4)  Alto costo o consumo variable

Al realizar la consulta obtendrá los datos de relevancia que se mencionaron anteriormente pero además cuenta con información sobre el Depósito.

### Consulta ítem por vencer 

Este tipo de consulta, lista la lista de productos con fecha próxima a vencer de acuerdo a una fecha que se puede ingresar como filtro.

## CONSULTA A.N.M.A.T

Son consultas propias del ANMAT. Se pueden realizar diferentes consultas entre ellas:

- Catálogo Electrónico Por G.T.I.N

- Catálogo Electrónico Por G.L.N

- Transacciones A.N.M.A.T

- **Transacciones No Confirmadas A.N.M.A.T**

- Médicos A.N.M.A.T

### Transacciones No Confirmadas A.N.M.A.T

Esta consulta permitirá al usuario obtener información de lo que podemos destacar: los Id. de transacción(del sistema y global), fechas, GTIN, n° lote, n° serie, Vencimiento,n° factura, n° remito. El usuario tendrá la posibilidad de filtrar por:

1)  Periodo de fecha.

2)  GLN origen y destino.

3)  n° lote y serie.

4)  Estado.

5)  Código de barra.

6)  Código GTIN.

## ÍTEM TRAZABLE

En esta sección se puede realizar consultas sobre los movimientos realizados solamente desde el sistema, tales como:

- Ítem Trazable: muestra la descripción del ítem, sus datos de recepción y el estado del ítem (pendiente, cuarentena, rechazado, validado)

- Ítem Trazable no validado: permite visualizar y modificar los datos de los ingresos de los ítems trazables que no fueron validados por ANMAT y el motivo por el cual no fue validado el mismo.

- Ítem Trazable por paciente: se pueden visualizar los eventos de los ítems trazables filtrando por paciente en un rango de fechas.

- Dispensación: se pueden visualizar los eventos de los ítems trazables filtrando por paciente o por ítem en un rango de fechas.

## MAESTRO FARMACIA

Esta consulta permite al usuario visualizar cada ítem y todos sus datos de relevancia como Unidad de Potencia, Unidad de medida, Potencia, monodroga, Genérico equivalente, tipo y subtipo de item, Vademecum y Acción Farmacológica.

Estos datos se podrán obtener filtrando por: Ítem, código de barra, monodroga, genérico equivalente, producto comercial o laboratorio.

## RECEPCIÓN COMPRA

Se pueden realizar diferentes consultas entre ellas:

- Recepción de compras

- Recepción de compras no migradas

- Recepción de compras con orden de compra

## PRECIO VENTA ITEM 

Posibilita consultar la vigencia de los Precios tanto como: precio venta unitario, compra unitario y Manual Farmacéutico. El usuario podrá filtrar por:

1)  Item

2)  Periodo de fecha

## CONSULTA NECESIDAD DE COMPRA

La consulta de necesidad de compra, le darán información al usuario sobre cuándo se realizó la necesidad de compra y quien la realizó, su origen, tipo, y además ver sus detalles. Esta consulta permite también visualizar: las solicitudes de cotización, solicitudes de órdenes de compra, órdenes de compra y las recepciones realizadas.

Se podra observar en distintos colores la prioridad de cada necesidad, es decir, en color ROJO las necesidades de prioridad alta, en NEGRO prioridad normal y en color VERDE las de prioridad baja.

## PROVISIÓN EXTERNA

Esta sección de consulta el usuario tiene la posibilidad de realizar la búsqueda por:

1)  Paciente

2)  Proveedor

3)  Entidad

4)  Ítem

En esta consulta el usuario extrae datos sobre ítems provisionados de forma externa, cantidad de provisión, cantidad disponible, proveedor, fecha.

## ÍTEMS EN CONSIGNACIÓN 

La consulta posibilita ver que items en consignación tiene la empresa en stock o con cantidad disponible. De la misma se podrá observar el proveedor, cantidad entregada, cantidad disponible, tipo de ítem; además se le permite al usuario hacer una consulta sobre la cantidad del ítem que consigna por paciente. Podrá filtrar por:

1)  Tipo y subtipo de ítem

2)  Consumo variable

3)  Proveedor

4)  Alto Costo

## PEDIDOS

Permite al usuario observar los pedidos por depósito de cada ítem para tener registro de la cantidad pedida y entregada de cada uno. Podrá filtrar la búsqueda por:

1)  Período de fecha

2)  Tipo y subtipo de ítem

3)  Ítem

ANEXO - BRECHAS FUNCIONALES
