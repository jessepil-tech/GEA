Fuente original: BP Depósito-Farmacia Configuraciones.docx
Ruta original: Farmacia/BP Depósito-Farmacia Configuraciones.docx
Formato original: DOCX

**BP- Depósito**

**(Configuración)**

versión 1.0

<table>
<colgroup>
<col style="width: 17%" />
<col style="width: 22%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr>
<th></th>
<th><strong>Título del Documento</strong></th>
<th>BP- Depósito (Configuración)</th>
</tr>
</thead>
<tbody>
<tr>
<td><p><strong>Información</strong></p>
<p><strong>General</strong></p></td>
<td><strong>Localización del Documento</strong></td>
<td></td>
</tr>
</tbody>
</table>

| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|----|----|----|----|----|
|  | Ale Samaniego Berenice | Soporte | Analista Funcional | 10/11 |
|  | Gareis Nazarena | Soporte | Analista Funcional | 14/11 |

| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|----|----|----|----|----|
|  | Escudero Ignacio | Operaciones | Gerente de operaciones | 22/12 |
|  | Claucich Catalina | Soporte | Analista Funcional | 22/12 |

| **Aprobado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|------------------|----------------------|--------------|---------|-----------|
|                  |                      |              |         |           |
|                  |                      |              |         |           |
|                  |                      |              |         |           |
|                  |                      |              |         |           |
|                  |                      |              |         |           |

| **Documento** | **Versión** | **Motivo del Cambio** | **Fecha Efectiva** |
|:-------------:|:-----------:|-----------------------|--------------------|
| **Historia**  |    1.00     |                       |                    |
|               |             |                       |                    |
|               |             |                       |                    |

Índice

[Objetivo del documento “BP – Depósito”: [5](#objetivo-del-documento-bp-depósito)](#objetivo-del-documento-bp-depósito)

1.  [Depósito [7](#depósito)](#depósito)

[Módulo: Administración General [7](#módulo-administración-general)](#módulo-administración-general)

2.  [Origen de Depósito [7](#origen-de-depósito)](#origen-de-depósito)

3.  [Stock depósito [9](#stock-depósito)](#stock-depósito)

[Administración de todos los depósitos. [9](#administración-de-todos-los-depósitos.)](#administración-de-todos-los-depósitos.)

[Ingreso a los depósitos creados por centro: [10](#ingreso-a-los-depósitos-creados-por-centro)](#ingreso-a-los-depósitos-creados-por-centro)

4.  [Configuraciones [11](#configuraciones)](#configuraciones)

[Depósito, Configuración inicial [11](#depósito-configuración-inicial)](#depósito-configuración-inicial)

[Personal de Depósito [13](#personal-de-depósito)](#personal-de-depósito)

[Personal que Autoriza las necesidades del Depósito [14](#personal-que-autoriza-las-necesidades-del-depósito)](#personal-que-autoriza-las-necesidades-del-depósito)

[Ubicación del Depósito [14](#ubicación-del-depósito)](#ubicación-del-depósito)

5.  [Habilitación de Tipos y Sub Tipos de Movimiento – [15](#habilitación-de-tipos-y-sub-tipos-de-movimiento)](#habilitación-de-tipos-y-sub-tipos-de-movimiento)

[Creación de sub-tipo de Movimiento Ingreso/Egreso. [16](#creación-de-sub-tipo-de-movimiento-ingresoegreso.)](#creación-de-sub-tipo-de-movimiento-ingresoegreso.)

6.  [Habilitación de Personal por Tipo de Movimiento [18](#habilitación-de-personal-por-tipo-de-movimiento)](#habilitación-de-personal-por-tipo-de-movimiento)

7.  [Transferencia de Depósito [18](#transferencia-de-depósito)](#transferencia-de-depósito)

8.  [Tipos de Movimientos por Defecto [19](#tipos-de-movimientos-por-defecto)](#tipos-de-movimientos-por-defecto)

9.  [Lugar de Entrega [20](#lugar-de-entrega)](#lugar-de-entrega)

10. [Habilitación [20](#habilitación)](#habilitación)

[Ítems en depósito. [22](#ítems-en-depósito.)](#ítems-en-depósito.)

11. [*Creación de Ítems de Farmacia* [24](#_Toc122602424)](#_Toc122602424)

[Para la creación de un ítem: [25](#para-la-creación-de-un-ítem)](#para-la-creación-de-un-ítem)

[Creación de Subtipo de ítem. [25](#creación-de-subtipo-de-ítem.)](#creación-de-subtipo-de-ítem.)

[Ítem Farmacia [26](#ítem-farmacia)](#ítem-farmacia)

[Costos de Recepción [29](#costos-de-recepción)](#costos-de-recepción)

[Precio de Venta [30](#precio-de-venta)](#precio-de-venta)

12. [Eliminación de un Ítem [31](#eliminación-de-un-ítem)](#eliminación-de-un-ítem)

13. [Configuraciones Manual Farmacéutico [32](#configuraciones-manual-farmacéutico)](#configuraciones-manual-farmacéutico)

14. [Configuraciones de Farmacia [35](#configuraciones-de-farmacia)](#configuraciones-de-farmacia)

15. [Equipo [38](#equipo)](#equipo)

16. [Genérico Equivalente [40](#genérico-equivalente)](#genérico-equivalente)

17. [Glosario [44](#glosario)](#glosario)

Objetivo

El proyecto de implementación al que este documento pertenece tiene como principales objetivos:

- Determinar las ventajas operativas del uso del módulo Depósito.

- Implementar el módulo de Depósito en el entorno del cliente, de una manera eficiente.

- Garantizar el uso correcto por parte del cliente, con relación al módulo Depósito.

## Objetivo del documento “BP – Depósito”: 

El presente documento tiene como objetivo principal documentar la configuración de los procesos que se van a llevar a cabo–haciendo uso del sistema al implementar– en las áreas incluidas en el alcance de este proyecto.

Alcance del Proyecto

Este documento se enfocará en los procesos establecidos para la implementación del módulo Depósito.

- Parámetros de configuración (todos los procesos)

Configuración de Módulo Farmacia

# Depósito

*Se ingresa por sistema, con usuario y contraseña. Dentro de los módulos que presenta ThinkSoft se selecciona el módulo Administración general que se encuentra en el primer renglón superior, en la segunda columna.*

## Módulo: Administración General

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image1.png" style="width:5.90556in;height:2.62982in" />

# Origen de Depósito

*Dentro del Módulo <u>Administración General</u> se pueden visualizar los menús,*

- Configuración General

- Configuración Operativa

- Dominios Médicos

- Dominios Enfermería

- Nomenclador

- Facturación

- Interfaces Migración

- ***<u>Módulos</u>***

> *Se debe ingresar al menú <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Stock</u>.*

- Farmacia

- Manual Farmacéutico

- Ítems

- ***<u>Stock</u>***

> *Por último en la sub selección <u>Stock</u> se extiende una nueva barra de opciones, en la cual se ingresa a la segunda opción, <u>Depósito</u>.*

- Movimiento Stock

- ***<u>Depósito</u>***

- Política Reposición Ítem

- Política Reposición Genérico

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image2.jpeg" style="width:5.90556in;height:2.67847in" />

Depósito

# Stock depósito

## Administración de todos los depósitos. 

*Al ingresar en el <u>Depósito</u> del lado izquierdo de la pantalla se extiende un desplegable en gris con las opciones de manejo de <u>Depósito.</u> Estas opciones se encuentran inhabilitadas hasta ingresar al Centro de Depósito que se va a configurar.*

- ***<u>Depósito</u>***

- Personal de Depósito

- Personal Autoriza Necesidad Depósito (anulada por el momento)

- Ubicación Depósito

- Habilitación de Sub Tipo Movimiento Ingreso

- Habilitación de Sub Tipo Movimiento Egreso

- Habilitación de Transferencia Depósito Ingreso

- Habilitación de Transferencia Depósito Egreso

- Habilitación Personal por Tipo de Movimiento

- Tipo de Movimiento por Defecto

- Habilitación Genérico Depósito

- Ítems en Depósito

- Lugar Entrega

*Abajo de estas las opciones se encuentran los botones de <u>Agregar</u> o <u>Eliminar</u>.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image3.jpeg" style="width:5.90556in;height:2.65972in" />

*La pantalla con los depósitos muestra tanto los <u>Centros</u> como los distintos depósitos habilitados que se encuentran en cada centro, para ingresar a uno de ellos se hace con un doble click en el elegido.*

### Ingreso a los depósitos creados por centro:

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image4.png" style="width:6.29314in;height:2.88357in" />

# Configuraciones

## Depósito, Configuración inicial

*Dentro de las acciones principales que se van a poder realizar en la Configuración de Depósito están:*

1.  Creación de depósitos.

2.  Habilitación del personal en el depósito.

3.  Modificaciones en los distintos depósitos.

*Una vez ingresado el centro las opciones de la barra en la izquierda se habilitan y desde ahí se comienza a configurar.*

*<u>Importante:</u> los \* marcan la obligatoriedad de los campos a completar.*

*Para configurar el <u>Depósito:</u>*

Se ingresa de manera obligatoria:

- Nombre del depósito.

- Tipo de impresora Entrega (si no se la configura sale la impresora asociada por default)

> En la segunda columna

- Sucursal (Empresa se encuentra asociado)

- Si el depósito no es 24hs y está abierto en cierto rango horario es obligatorio escribir el rango del mismo.

> En la tercer columna

- Cantidades

Es importante completar: GLN de producto y el GLN de medicamento y los usuarios y las contraseñas para poder realizar la trazabilidad con ANMAT. Esto se debe realizar para cada depósito.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image5.jpeg" style="width:5.90556in;height:2.65972in" />

Se define:

1.  La impresora que realiza la impresión de la preparación y la de la entrega, con la cantidad de impresiones que realiza.

2.  La configuración del punto venta remito el cual es el número del comprobante interno al cual se le asignan los remitos de las transferencias realizadas. Este valor por defecto está en “1”.

3.  Si el depósito genera necesidades de compras o realiza recepciones de compras diferidas. Además, si al realizar las necesidades de compra, requiere autorización por parte de algún personal (si requiere, se deberá configurar el personal que autoriza las necesidades de compra en el menú Personal Autoriza Necesidad Compra).

4.  Si requiere confirmación de transferencia entre depósitos. La confirmación permite o no el ingreso a stock inmediato.

5.  Si el depósito funciona 24hs. En caso de tener horario reducido, se lo debe indicar para que los pedidos que recibe el depósito sean dentro dicho horario. Fuera de este horario, se enviarán al depósito secundario.

6.  Si el depósito realiza el fraccionamiento automático de ítems cuando se realiza la recepción de los mismos.

7.  Si realiza la preparación y la entrega de medicamentos en el mismo momento (preparación y entrega unificadas).

8.  La habilitación de ítem en dep remoto. Esta configuración permite que, si el ítem no está habilitado en el depósito destino, lo habilita automáticamente.

9.  En caso de que sea un depósito de terceros, se debe configurar el webservice.

10. Si el depósito puede realizar entregas directas a pacientes internados. En desuso.

Se configura<u>:</u>

> La cantidad de minutos en la que suena la alarma, en la pantalla de preparación de pedidos, cuando hay pedidos pendientes.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image6.png" style="width:6.13077in;height:3in" alt="https://lh5.googleusercontent.com/0UA2wGsIz975c5J8N8hGb5Dq4D6MWsh4uJddvUovESvVk_g8P25JnRMc1jCrWpPZYSZbOdjSuHok7cN5NuUzH_wFIjJ3AUaZTsxvadLOLYKkBDnMfsSns14yqttmODodSmivqykWcuKGOtERMWJQX1-dHzLHFBwbf28BQae7zUxwVb_heoifPBuQiGzf-g" />

*En caso de agregarse un nuevo depósito, las opciones del panel de configuraciones a la izquierda se bloquean, hasta completar toda la información referente al nuevo depósito y la asignación de parámetros generales.*

## Personal de Depósito

Se definen las personas asociadas al depósito creado que serán los que despúes se visualizan en el desplegable de la sección *<u>Personal de Depósito</u>. En esta pestaña se agrega el personal que se encontrará habilitado para acceder al depósito. El mismo puede ser eliminado con el botón que se encuentra a la derecha en caso de ser requerido.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image7.png" style="width:6.0707in;height:2.90479in" alt="https://lh5.googleusercontent.com/s21wmPg-knNuvN7Ye83_6GJEGilZu-Ep-d9MFFQiyt-tIh_GkPyxCI6f453W3vBYCcvqIZKnr1geYuOgMT2nEYivd9owE7m6kW0kzLO8n8nPiz6zFbDj0YP77-J03RIOGlLXlAegH3nUveBMNnfJ_klBlnAddLNBnOXYcrPYUDrYmcvMK8JYtW_zwosftw" />

## Personal que Autoriza las necesidades del Depósito

Se configuran las personas que administran y autorizan las necesidades del depósito. El primer usuario que lo haga será quien quede a cargo de esa autorización.

Se puede ver el personal, quien le dio de alta y cuál fue la fecha de alta.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image8.png" style="width:6.14615in;height:3.01459in" alt="https://lh6.googleusercontent.com/2VYQi2jEETd68vc9SMAftOLoaFLvqh7aOCT6UdFls4L7oveQR1AtBXF81bLF2kD2rvsityZeP6cUSlzEDJ68rpt470Wnt4sXM-UJ-YFSKw5fv9UvroRWpusw3-qWAIdOavj0V6Y5vBcfC1lJeaZp-4-ed9jFR3Jzvh9cyZOO7T-JBDCR84B9utJxziJK2w" />

## Ubicación del Depósito

Se define la ubicación física donde se pueden encontrar los ítems dentro del depósito, como lo es un sector en una estantería o un cajón.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image9.png" style="width:6.15385in;height:3.03073in" alt="https://lh6.googleusercontent.com/-qywka4yEZJaHB_TU45hVVq_vkpOIxDudPz1SZsBjkKZuI5b2HLkpG0ikSALy3WANfrc-0wqCzizm2F3Nx5ZfneRDOChEBATIToub3iWF5bYeBz3dhKFsawmllbUHAbiMc1OSYBAvohQopRjvc4tjlodZxQy8mEBFWFVPvko-oiQyAImf_TVS3mt2Hn1ag" />

# Habilitación de Tipos y Sub Tipos de Movimiento –

#### Ingresos y Egresos

Se visualizan y se pueden agregar los tipos y subtipos de Ingresos/Egresos que se encontrarán habilitados en el depósito.

Se le agregan los Conceptos Contables (se encarga contaduría) y si se requiere Habilitación Adicional para realizar dichos movimientos. A la derecha de la pantalla, sobre la columna acciones, se pueden editarlas o eliminarlas si es necesario.

En caso de que se configure que se requiere habilitación adicional para realizar determinados movimientos, se deberá cargar el personal habilitado para realizar dichas acciones en el menú “Habilitación de personal por tipo de movimiento”.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image10.png" style="width:5.90696in;height:2.84615in" alt="https://lh5.googleusercontent.com/WD8bEXkXkGN-zVslB4mYg1etptWndUmzx2oyqBUrXgjELA__DqDblYl1AfcSVDNKXeAnevnyP8F1ibDYEVbRyQUsAl56iX9tk9PPUJLnr6y-xxhhBwI83TrGlC4DxZwMjZ8HgnmBVwj2qDfUF55w6UKxheM0wd_9pvqCVv1aolJ0UVNeYFQ-PKuO_a35dg" />

A la izquierda de la pantalla se encuentra la opción de editado de <u>Habilitación de tipo y subtipo de Ingreso/Egreso de movimiento</u>.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image11.png" style="width:6.28275in;height:2.96816in" />

## Creación de sub-tipo de Movimiento Ingreso/Egreso.

> *Para crear un tipo de Ingreso o Egreso se ingresa nuevamente por <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Stock</u>.*

- Farmacia

- Manual Farmacéutico

- Ítems

- ***<u>Stock</u>***

> *Por último en la sub selección <u>Stock</u> se extiende una nueva barra de opciones, en la cual se ingresa a la segunda opción, <u>Movimiento Stock</u>.*

- ***<u>Movimiento Stock</u>***

- Depósito

- Política Reposición Ítem

- Política Reposición Genérico

*Al clickear en el botón de abajo a la izquierda que dice **<u>+Agregar</u>** se habilita la pestaña <u>Sub Tipo Movimiento de Stock</u> abriendo una pantalla donde el <u>Tipo Movimiento Stock</u> es desplegable y con opciones predeterminadas. Por debajo el <u>Sub Tipo de Movimiento Stock</u>, también es obligatorio pero en este caso es libre escritura de quien configura. Por último se define si es <u>Ingreso</u> o <u>Egreso</u>.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image12.jpeg" style="width:5.90556in;height:2.67708in" />

Los movimientos de ajustes son los realizados por el usuario para igualar el stock que figura en el sistema con el stock real.

Los movimientos de consignación son los ingresos y egresos de los ítems bajo consigna de parte de proveedores.

Los movimientos de pacientes son los ítems que se administran y devuelven en nombre de un paciente específico. Un egreso de movimiento de paciente es cuando se realiza la administración de enfermería del ítem. Un ingreso es cuando se realiza la anulación de un ítem administrado. *(\*)*

Los movimientos de préstamo y devolución son los ítems que llegan a préstamo desde otras instituciones.

Los movimientos de proveedor son los movimientos de compras.

Los movimientos de provisiones externas son los movimientos de pacientes, entidades y proveedores que envían ítems reservados a pacientes específicos.

Los movimientos de servicios son los movimientos que se realizan a los servicios dentro de la institución (ej: cardiología)

Los movimientos a áreas organizacionales son los movimientos que se realizan a las áreas organizacionales dentro de la institución (ej: librería).

*(\*) Los movimientos que se realizan en reserva de paciente desde la farmacia al office no son movimientos de pacientes. Dichos movimientos están agrupados dentro del movimiento de transferencia.*

# Habilitación de Personal por Tipo de Movimiento

*Como se menciona anteriormente, en caso de tener tildado que un movimiento requiere la Habilitación del Personal, se tiene que completar la Habilitación de Personal por Tipo de Movimiento.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image13.png" style="width:6.19827in;height:2.94615in" alt="https://lh3.googleusercontent.com/QP2t9scEsI0OV8rWQcuq1Dc2ls2MxzSiX5poBfY5dHN-ZZE8LoHHrA_2CP5OSKNvER7GdeGaE4rg64Wup6CY0kmQpMgI6AzgEVB-19hsIw6fmu-gtCbaUjYxvpXLWPJEDDnco_cfUYSxixk95NYB5uvcmoqxro6oPRG9ENUjrOakoPMsoxXSGmVu3XovIA" />

# Transferencia de Depósito

#### Ingreso / Egreso

*En esta pestaña se habilitan los depósitos a los cuales se pueden realizar Transferencias ya sean de ingreso o egreso.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image14.jpeg" style="width:5.90556in;height:2.67847in" />

*Para habilitar el movimiento de ingreso de ítems provenientes de un determinado depósito, se debe agregar el depósito haciendo click en el botón en la parte inferior de la pantalla donde dice **<u>+Agregar.</u>***

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image15.png" style="width:6.09231in;height:2.91538in" alt="https://lh6.googleusercontent.com/jAKT7urNoYxO4H9BoftVTUftRFHb4e9xSf9aVo9OZAK-u_XiYnPNyUSBGUWWhp6_c6FPmZGRGrIEIgzqZMCn_qqGO0Xmv3vXsfjrzVbtAcp5xInTjEK2e_BpimMCmeyo8JArP5jsrP8WYNsufISz1-JsHFrsrZN3MwI_zT8HW6klDivodS5v-oEajlQdnQ" />

# Tipos de Movimientos por Defecto

*Para cada <u>Tipo de Movimiento</u> se establece <u>Tipo y Subtipo de Movimiento de Stock</u>. Los movimientos que no se configuren en este menú no podrán ser realizados desde el depósito.*

- *Ej: Ingreso: Compras -\> Proveedor Compras*

- *Ej: Egreso: Devolución de Compras*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image16.png" style="width:6.26154in;height:2.96154in" alt="https://lh6.googleusercontent.com/PSnGgUsGjCDCX6KoAWMEHXeqf-yN2wd4j6-73_uO_H-2rr4dS7y_fu48ZubKaTqxcDoDg7hyrRf9bsVioJ9qkjiQ-mM6UGK7OmrCGAcuCyhVYCsHoyiPy8ED8qpGUJaQQj1OfgswnShEloVHIX_cvoXNFd_sstYDhyqF9xy_Vmgv5IviXtnPUxGP9FML0g" />

# Lugar de Entrega

Se ingresa la dirección y contacto que va a salir en la orden de compra, tiene que tener siempre un lugar de entrega asignado. Puede tener uno o más lugares de entrega pero **<u>NO puede No tener un lugar asignado</u>.**

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image17.png" style="width:6.26154in;height:2.98462in" alt="https://lh5.googleusercontent.com/LGgjBGFVq6lX2yc6QtcUROP7jDJBdv3yARLDDkk0z0vLSrL_lMkcUOBGziLdlGESxcxlgwKsAL5UkpEsp7xvR7U1w2Br55MG_ep6gK61GbEKGH_mKw9zIHIF9p3sakhcmsBoKdtIT47eydxWkQJkJLNTTi89jB6xYoBSvvfg_2RfDXwTjr2muoKyqCofhw" />

# Habilitación

*La habilitación de Ítems puede ser por:*

#### Por Genérico (MANUAL FARMACÉUTICO)

En el menú lateral “Habilitación Genérico Depósito” se habilitan los genéricos con los que se trabajarán en el depósito. Al habilitar un genérico, se habilitan en el depósito los ítems comerciales que estén asociados al mismo. Cada ítem *tiene* que estar habilitado en el depósito para poder realizar las recepciones y transferencias de los mismos.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

En caso de querer excluir ítems comerciales que fueron incluidos al realizar la habilitación por genérico, se deben agregar en el menú “Exclusión por depósito de producto comercial”

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Ítem del Depósito 

En este menú se habilitan los ítems por producto comercial.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image20.png" style="width:6.2618in;height:3in" alt="https://lh5.googleusercontent.com/w3LQGtSAdudPyDsyl5iY_4to8NSZCXfY0YI16y8ir1j6azsXQENNSPY44OVclVEIdQ0ZJokdQ2bjaMVg0JSiIe6EdOIiaRlDF-Fe0KJhCwXEh0-oVO0IV0uyJj4eAtkGak5yXr8pt-EAyOzZ7_xf6LGK9WKWffYU4s_GXSZvYed6eibDHj4hpMqxsLS4ew" />

*En un mismo depósito se pueden configurar habilitaciones por genérico o por producto comercial simultáneamente.*

## Ítems en depósito.

En los ítems habilitados se pueden hacer habilitaciones particulares. En esta pantalla figuran todos los ítems comerciales que habilitamos previamente (los ítems comerciales habilitados por genérico y los habilitados por ítems del depósito). Si el ítem es multidosis, como se factura y además, la ubicación en el depósito (lugar físico donde se encontraría cada ítem) en caso de definirla.

> *<u>Checkbox:</u>*

- *Afecta Stock: se permiten realizar movimientos del ítem hacia otros depósitos.*

- *No Afecta Stock:* al ingresar un ítem al depósito el cual no afecta stock, el mismo se da por consumido y no permite hacer movimientos como transferirse.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image21.png" style="width:6.26176in;height:2.96923in" alt="https://lh3.googleusercontent.com/i4gtKpZoVckJ1RDnLF6kc6EDbsKUqDpG9-aJtZVn1rtVpkLdnVImNJL1ik-WQdTOm6Vq6AteOB9NMYLsOzuOf9yU36XZH-aIMezJFUyza9lGJVOxdnIoR_x8e8ziu1iNUgqWZ5qnn9wspQmD1Bwpu3EfwNVOVlQ3Z6Xb_YazSCY7VFArU64kt4U2vO4fjw" />

<span id="_Toc122602424" class="anchor"></span>*  
*

# *Creación de Ítems de Farmacia*

> *Para crear un Ítem de Farmacia se ingresa nuevamente por <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Stock</u>.*

- Farmacia

- Manual Farmacéutico

- ***<u>Ítems</u>***

- Stock

> *Por último en la sub selección <u>Ítems</u> se extiende una nueva barra de opciones, en la cual se ingresa a la segunda opción, <u>Ítems Farmacológicos</u>.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image22.png" style="width:5.90278in;height:2.63889in" />

Se busca cualquier producto comercial, creados de forma autónoma y por Alfabeta.

### Para la creación de un ítem:

1.  Tipo de ítem: es fijo. Puede ser: medicamento, compuesto o descartable.

2.  Subtipo: se puede modificar, agregar o crear por el usuario.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image23.png" style="width:6.18498in;height:3.00769in" alt="https://lh6.googleusercontent.com/gw2LP7GwXAQPO_o06tsL42UVBVS5O7ONclGPm_2Onxo_vn8LkHqNXo8gIcB28vTttOOiCSN2rgDwQXtRnaRmOTMVaQRqnk04jgJldgTLRraict_pgTQHZHRtOcRdx10G7uXc5wRxU6WbukSJyYU6OOxf9hZ0mnZaJUBZCc5uoJFYLMdKrW2nUdOMCIJG3A" />

## Creación de Subtipo de ítem.

Es importante decidir si Autogenera Código de ítem. Esto hace que, al crear un ítem farmacológico, se genere automáticamente el código al nuevo tipo de ítem creado. En esta pantalla, además, se realizan configuraciones que corresponden al área de compras.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image24.png" style="width:6.26178in;height:2.99231in" alt="https://lh6.googleusercontent.com/jchLSCx_XuuOySwYcn-5BJbB_hRSVIsKKdQqIe34qTlVzztjIzeM_VuMepq6xz3Slyuo_nw0X3pWDchQqTaQhCFoAb66gmMJ98hjK-zDsbLSXbZM-xpD7cB_hpzXOagIaP5KR4rJ_sYBSUcrnvLOlG8C_xW8MH8TGEC3z_2IXY9y9qCzw-OayVKsFDeF3Q" />

## Ítem Farmacia

Se muestra la siguiente información.

1.  Tipo: si es medicamento, descartable, compuesto.

2.  Sub-tipo de ítem. Se despliegan los subtipos según el tipo seleccionado.

3.  Código de ítem farmacia. Si el código es autogenerado, se marca automáticamente este checkbox al momento de la creación del ítem.

4.  Producto comercial. Al definir este punto, se carga el laboratorio fabricante. Los laboratorios son configurables por el usuario.

5.  Presentación. Es un campo de texto libre donde se realiza la descripción del ítem (droga, potencia y unidad de potencia, forma farmacéutica y cantidad fraccionable/fraccionada).

6.  Tipo de Venta (libre, bajo receta, etc.).

7.  Monodroga

8.  Genérico equivalente asociado al ítem.

9.  Si el ítem pertenece al Vademécum Hospital.

10. Si es un fraccionado. En caso de no serlo, se debe especificar la cantidad fraccionable y si se dispensa no fraccionado (administra a paciente).

11. Trazabilidad. Si el ítem es trazable se debe marcar esta casilla y configurar la trazabilidad correspondiente explicada más adelante en el manual.

12. Ítem por unidad de tiempo y la unidad de tiempo correspondiente: se utiliza para especificar si un medicamento se debe administrar de manera continua.

13. Si la carga del ítem fue realizada por alfabeta se tilda automáticamente este check. También se muestra el código del ítem de alfabeta asociado. Si el ítem está dado de baja en alfabeta se tilda automáticamente dicho checkbox.

14. Si el ítem requiere refrigeración, es un ítem importado, si es de alto costo, consumo variable, exento de iva o si es un extra (para facturación: el paciente pasa por caja para pagarlo).

15. Tamaño de ítem farmacológico (meramente informativo)

16. Vía de administración (ej: oral, local, etc.).

17. Unidad de medida y unidad de medida en la que se fracciona (meramente informativo, por ej para descartables que llevan medidas como los parches).

18. Potencia y unidad de potencia de la monodroga.

19. Número de troquel.

20. Control del ítem farmacia: (no controlado, estupefacientes, psicotrópicos o venta vigilada).

21. GTIN.

22. Código externo en caso de que se utilice una interface.

23. Código de barra (si el ítem no viene de alfabeta se puede realizar la carga manual).

24. Material especial cirugía (informativo)

25. Acción farmacológica.

26. Si el ítem está habilitado para la dispensación (se administra a paciente) y para su compra (por ej para productos médicos como prótesis que deben estar configurados pero que no compra el sanatorio).

27. Si es multidosis. Se debe configurar la cantidad de dosis aproximadas que contiene el ítem y la cantidad de dosis de reposición.

28. Forma farmacéutica. Allí se debe configurar también la cantidad de la presentación (ej: jarabe, comprimido, etc.).

29. Potencia del volumen y su unidad. La potencia del volumen representa el volumen total de la presentación del ítem (ej: jarabe de 90ML: potencia volumen 90) Esto se configura para los ítems que son soluciones o cremas.

30. Activo. Si se desactiva este campo, se cargan automáticamente el usuario y la fecha en la que fue desactivado.

31. Ítem farmacia base precio (se carga cuando se realiza la definición del fraccionado). Es el ítem del cual toma el precio el medicamento fraccionado.

*Opciones parametrizables por el usuario. Se pueden crear nuevas desde el menú Manual Farmacéutico.*

*  
*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image25.png" style="width:5.90069in;height:2.79236in" />

**30**

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image26.png" style="width:5.90069in;height:2.80694in" />

**30**

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image27.png" style="width:5.90069in;height:2.78681in" />

#### Configuración de trazabilidad.

La configuración de trazabilidad puede estar dada por:

- ***<u>ANMAT:</u>*** trazabilidad de producto (opción ANMAT_PRODUCTO) o medicamento (opción ANMAT_MEDICAMENTO).

- ***<u>Interna:</u>*** nro de lote, nro de serie o fecha de vencimiento

También se debe indicar la fecha de inicio de trazabilidad. Esto se configura para que, si existe un stock de este ítem que no está trazado, no le afecte.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image28.png" style="width:6.26176in;height:3.00769in" alt="https://lh6.googleusercontent.com/W8ar86m3pEX3QIvZDPD7jMrq8XWDXmCuISAGTG_xPcvw9h40C67iC2o_7MHlPt8ap-tKFqz0kLD3vvhZZ1CCmd0ar77kcnVqgE7eKKW0OPY3BPqyH7cY1TqE7SqwyYtuuJC7aJWCjBItsikXBPTfOkCjWgOuoU5tXkWUGnIkTxwv2OeAq0xwulr6YSmNnw" />

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image29.png" style="width:6.26178in;height:2.99231in" alt="https://lh3.googleusercontent.com/xh9sTli2g3lIjZ0gXNvbcprIaJ1JAnqR46YSNdiUsXZXN1DIKg2R6knfFXcc2RR0oskXraiPGLT42E_Ht35ZRi_CX7Ucml56RfNo6VOCgB3bHOBCNcqkB89StP3KI_6TxUBXl7plfQ11McskowBda7HpVB7HOLpGz00LoGRtGgRe5WGjfQTsftAkqcbk-w" />

### Costos de Recepción

Se guarda el Histórico de los costos de reposición, en el menú lateral denominado “Costo de reposición”. Los mismos se listan del más actual al más antiguo, junto con la fecha de la vigencia, la forma de actualización, quién actualizó y la fecha en la que fue realizada.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image30.png" style="width:6.26178in;height:2.97692in" alt="https://lh3.googleusercontent.com/Nv0BHqCCNhUS2YaWou3lIzZ0eAb1XbRE91bAwV8K2YFKQV85She-4ByZiXPwadKSpbbK4U9l-W6c8W-NSv8hvIlTamT8wvTWzqSMZiZNubOMw4P_S17WwMU1T8VMc3FNXiNH3FDVUMXIRmnwyrVanY01h0uUtNewApDwpkmh3hSFB_SzFWrliuyRaJwYQQ" />

### Precio de Venta 

En el menú lateral “Precios de venta” se listan los precios de venta. Existen dos tipos de precio de venta:

1.  Precio Venta: es el costo de reposición más el Mark Up.

2.  Precio Venta Manual Farmacéutico: se determina a partir del producto asociado a alfabeta y se actualiza automáticamente cuando se realiza la actualización de alfabeta.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image31.png" style="width:6.28234in;height:3.00052in" />

# Eliminación de un Ítem 

La posibilidad de eliminar un producto es si está recién realizado, una vez q está asociado con órdenes de compra u otros movimientos. <u>No se puede eliminar,</u>

**<u>Se puede inhabilitar destilando:</u>**

1.  Vademécum hospital

2.  Activo

3.  Dispensa No Fraccionado o Habilitado Dispensación

4.  Habilitado para Compras

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image32.png" style="width:5.9in;height:2.83056in" />

# Configuraciones Manual Farmacéutico

> *Para realizar las configuraciones de las monodroga, vías de administración, unidades de potencia y forma farmacéutica se ingresa nuevamente por <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Manual Farmacéutico</u>.*

- Farmacia

- ***<u>Manual Farmacéutico</u>***

- Ítems

- Stock

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Monodroga

En el campo Monodroga se especifica el nombre de la droga, mientras que en el campo Monodroga Indica se especifica el nombre con la que el médico visualizará la droga al momento de realizar una indicación.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Vía de administración

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Unidad de potencia

En este apartado, se pueden configurar las unidades que acompañan a todas las potencias como son las unidades de potencia de las dosis indicadas o administradas.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Forma Farmacéutica

En esta pantalla se configuran todas las formas farmacéuticas características de los ítems.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

# Configuraciones de Farmacia

> *Para configurar los Laboratorios y la acción farmacológica se ingresa por <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Farmacia</u>.*

- ***<u>Farmacia</u>***

- Manual Farmacéutico

- Ítems

- Stock

#### Laboratorio

Para configurar los laboratorios se ingresa al menú “Laboratorio Fabricante”.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

En el campo “Laboratorio Fabricante” se completa con el nombre del laboratorio.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

En el menú lateral “Producto Comercial” se deben agregar todos los productos comerciales que dicho laboratorio fabrica.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Acción Farmacológica

Para configurar las acciones farmacológicas (ej: analgésico, antibiótico, etc) se ingresa al menú “Acc Farmacológica”.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

# Equipo

> *Para crear un equipo se ingresa nuevamente por <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Stock</u>.*

- Farmacia

- Manual Farmacéutico

- ***<u>Ítems</u>***

- Stock

> *Por último en la sub selección <u>Ítems</u> se extiende una nueva barra de opciones, en la cual se ingresa a la segunda opción, <u>Equipo</u>.*

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

Se debe configurar:

1.  Tipo: si es medicamento, descartable, compuesto.

2.  Sub-tipo de ítem. Se despliegan los subtipos según el tipo seleccionado.

3.  Código de ítem.

4.  Equipo: nombre del equipo

5.  Tipo Equipo: opciones configurables en el menú configuraciones -\> ítems -\> tipo equipo.

6.  Marca producto: opciones configurables en el menú configuraciones -\> farmacia -\> marca producto.

El resto de las configuraciones se explicaron previamente en la creación de ítem comercial.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image43.png" style="width:5.90556in;height:2.77905in" />

**6**

**5**

**4**

**3**

**2**

**1**

# Genérico Equivalente

> *Para crear un Genérico Equivalente se ingresa nuevamente por <u>Módulos</u> donde se despliega una barra de opciones y en la misma se selecciona la tercera opción, <u>Depósito</u>*

- Turnos

- Admisión

- ***<u>Depósito</u>***

- Compras

- Acreditación de Profesionales

- Contable

- Cobranza Convenios

- Cirugía

- Infectología

- Internación

- Facturación

> *Dentro de la selección <u>Depósito</u> se despliega nuevamente una barra de opciones, donde se ingresa a la última de esta, <u>Stock</u>.*

- Farmacia

- Manual Farmacéutico

- ***<u>Ítems</u>***

- Stock

> *Por último en la sub selección <u>Ítems</u> se extiende una nueva barra de opciones, en la cual se ingresa a la segunda opción, <u>Genérico Equivalente</u>.*

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image44.png" style="width:5.90556in;height:2.7169in" />

Se debe configurar:

1.  Genérico equivalente: nombre del genérico

2.  Código de barra

3.  Activo: si el genérico está activo

4.  Auto calculable.

5.  Trazabilidad

6.  Multidosis

7.  Drogas que contiene el ítem. Se debe completar la Potencia y unidad de potencia asociada a la monodroga correspondiente.

8.  Potencia del solvente y unidad de potencia del solvente. Se corresponde con la unidad del volumen en cual se encuentran disueltas las drogas configuradas en el punto 7.

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image45.png" style="width:5.90556in;height:2.79241in" />

**6**

**5**

**4**

**1**

**3**

**2**

<img src="/mnt/data/.tmp_farmacia/Farmacia_Markdown/.media/media/image46.png" style="width:5.90556in;height:2.79107in" />

**7**

#### Configuración de auto-calculables

Los ítems auto calculables son aquellos en los cuales el sistema calcula automáticamente la cantidad utilizada. Se pueden configurar de acuerdo a si los mismos son calculados de acuerdo a la cantidad de personas o la cantidad de tiempo.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

Seleccionando el “Tipo Auto Calculable” Cantidad personas se debe completar el campo Ctd Personas, el cual se corresponde con la cantidad de personas para el cual se calcula el uso del ítem.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

Seleccionando el “Tipo Auto Calculable” Tiempo se debe completar el campo Unidad de Tiempo, el cual puede ser horas o minutos y la Ctd Unidad de Tiempo, la cual determina la cantidad de horas/minutos en la que el ítem es consumido.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

#### Configuración de multidosis

Se debe configurar al mismo como una solución tildando dicho checkbox

Se debe configurar al mismo como multidosis tildando dicho checkbox

Ej: amoxicilina 500mg/5ml jarabe

Debe contar con:

- Potencia, (500)

- Unidad de potencia, (mg)

- Monodroga, (amoxicilina)

- Potencia Solvente, (5)

- Unidad de Potencia Solvente, (ml)

- Forma Farmacéutica (jarabe)

#### Conversión Ítem Genérico

Se puede utilizar la conversión a Ítem genérico para poder transformar cualquier marca comercial comprada al ítem genérico creado en las recepciones de compras, generando el movimiento de fraccionamiento por el ingreso de la marca al ítem elegido.

Luego se debe buscar el ítem al cuál se convertirá dicho genérico (sólo puede ser un ítem del mismo genérico cuya cantidad fraccionable sea la mínima unidad, es decir 1).

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

# Configuración Parámetros Generales

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

En esta sección se configuran los subtipos de movimientos de stock para los fraccionamientos y el control de inventario. Además, se indica cuál es el prefijo del fraccionado.

# Glosario

*Depósito:* toda ubicación física que almacene ítems y que se deba controlar su stock (ej: office de enfermería, farmacia central, carro de paro, etc.)

*Servicio:* servicios médicos de la institución que requieran consumir ítems. No se controla stock de los servicios.

*Área organizacional:* áreas administrativas que requieran consumir ítems. No se controla stock.
