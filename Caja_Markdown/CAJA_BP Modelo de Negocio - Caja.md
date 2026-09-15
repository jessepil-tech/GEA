Fuente original: BP Modelo de Negocio - Caja.docx
Ruta original: Caja/BP Modelo de Negocio - Caja.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image1.png" style="width:2.94792in;height:0.5in" />

BP – Modelo de Negocios HIS– Caja

Versión 1.0

# *Información del Documento*

|  |  |  |
|:---|:---|:---|
|  | **Título del Documento** | BP – Modelo de Negocios – Caja |
| **Información General** | **Localización del documento** |  |

|  |  |  |  |  |
|:---|:---|:---|:---|:---|
| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Lic. Romina Makuch | Soporte funcional | Analista Funcional | 27/12/2022 |
|  |  |  |  |  |

|  |  |  |  |  |
|:---|:---|:---|:---|:---|
| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Ing. Catalina Claucich | Implementación | Líder de proyecto | 28/12/2022 |
|  |  |  |  |  |

|                  |                      |             |         |           |
|:-----------------|:---------------------|:------------|:--------|:----------|
| **Aprobado por** | **Nombre (Empresa)** | **Proceso** | **Rol** | **Fecha** |
|                  |                      |             |         |           |
|                  |                      |             |         |           |
|                  |                      |             |         |           |

|               |             |                       |                    |
|:--------------|:------------|:----------------------|:-------------------|
| **Documento** | **Versión** | **Motivo del Cambio** | **Fecha Efectiva** |
| **Historia**  | 1.0.        |                       |                    |
|               |             |                       |                    |

[*Información del Documento* [2](#información-del-documento)](#información-del-documento)

[Alcance general [4](#objetivos)](#objetivos)

[Consulta ordenes de caja pendientes de cobro [6](#consulta-ordenes-de-caja-pendientes-de-cobro)](#consulta-ordenes-de-caja-pendientes-de-cobro)

[Ingreso/Egreso de adicionales de caja [9](#ingresoegreso-de-adicionales-de-caja)](#ingresoegreso-de-adicionales-de-caja)

[Ingreso/Egreso de fondo de cambio [9](#ingresoegreso-de-fondo-de-cambio)](#ingresoegreso-de-fondo-de-cambio)

[Cobranza en cuenta corriente [10](#cobranza-en-cuenta-corriente)](#cobranza-en-cuenta-corriente)

[Devolución al paciente de saldo en cuenta corriente [11](#devolución-al-paciente-de-saldo-en-cuenta-corriente)](#devolución-al-paciente-de-saldo-en-cuenta-corriente)

[Cancelación de comprobantes con reintegro [12](#cancelación-de-comprobantes-con-reintegro)](#cancelación-de-comprobantes-con-reintegro)

[Cambio tipo de valor [13](#cambio-tipo-de-valor)](#cambio-tipo-de-valor)

[Consulta movimientos de caja [14](#consulta-movimientos-de-caja)](#consulta-movimientos-de-caja)

[Pago adelantado [14](#pago-adelantado)](#pago-adelantado)

[Rendición de caja [15](#rendición-de-caja)](#rendición-de-caja)

# 

# 

##### Objetivos

En el siguiente documento se describirán los procesos asociados a la gestión de Caja, contemplados en el sistema Thinksoft HIS.

Es importante aclarar que el módulo de Caja está dividido en procesos detallados que quedan descritos mediante su correspondiente diagrama de negocios (BPMN) en este documento.

**Objetivo del documento “BP – Caja”:**

El presente documento tiene como objetivo principal documentar y *aprobar* todos y cada uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con otros sistemas informáticos implementados en la institución.

La correcta documentación de estos procesos está sujeta a las reuniones continuas con los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir, así como las oportunidades de cambio y mejora. Cada proceso plasmado en este documento debe ser aprobado por el referente designado, y todo aquello que no se registre adecuadamente en esta instancia puede suponer una demora en los tiempos de implementación a futuro.

##### 2. Alcance del Proyecto

El presente documento tiene como finalidad explicar todos los procesos correspondientes a la Caja. El propósito del módulo de caja abarca todas las operaciones relacionadas con el movimiento de dinero respecto de la atención al paciente

# Alcance general 

#### 

El propósito del módulo de caja abarca todas las operaciones relacionadas con el movimiento de dinero respecto de la atención al paciente. Existen dos tipos de tratamiento para la caja:

- <u>Caja integrada</u>: las operaciones relacionadas con la caja se encuentran dentro del módulo de recepción y las operaciones de ingreso/egreso se desarrollan dentro de ese mismo módulo.

- <u>Caja no integrada</u>: el paciente se recepciona y las operaciones relacionadas con los diferentes ingresos/egresos de fondos se realizan en cajas que no dependen directamente de la recepción.

**Independientemente de cual sea el tratamiento de las cajas, pantallas son las mismas y la visualización de cada una de ellas puede ser parametrizable mediante permisos.**

##### **Consulta ordenes de caja pendientes de cobro**

Son aquellas órdenes de servicio que se encuentran pendientes de cobro y que vienen desde el módulo de recepción. Al ingresar a la pestaña, se listarán de manera automática aquellas que se encuentren pendientes y se visualizarán datos como horario, nombre del paciente, y monto. Se asemeja a una bandeja de entrada en donde se listan cada una de las órdenes pendientes de cobro.

Además, se permite realizar cobros correspondientes de otros puestos marcando una opción dentro de la pantalla llamada “Ver ordenes de otras cajas”. Es decir, el sistema permite realizar operaciones de otras cajas de ser necesario.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image2.png" style="width:6.65347in;height:2.99722in" />

Al seleccionar una orden de cobro, se visualizará la pantalla de carga de datos y valores.

Dependiendo de la configuración del puesto, se podrá recibir tanto efectivo como transferencias o cheques, al igual que diferentes divisas, como dólares o euros. Al realizar un pago con moneda extranjera, el sistema calculará automáticamente su valor en pesos utilizando la cotización cargada en el sistema.

**Los campos obligatorios para cargar un valor estarán marcados con (\*).**

<u>Proceso</u>

- Ingreso a la orden pendiente

- Valores a ingresar: son los medios de pago con los que efectivamente se irá a cobrar. Tipos de valores -\> EFECTIVO, TARJETA, BANCO (TRANSFERENCIA)

- Moneda: diferentes monedas habilitadas para su uso dentro de la empresa

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image3.png" style="width:5.90347in;height:1.05833in" />

**<u>Cobros con Tarjeta</u>**

Los tipos de tarjeta a utilizar para realizar cobranzas deben ser configurados previamente en el sistema desde la “Configuración - Empresa”. El número de cupón es el correspondiente al que entregue el punto de venta.

De ser necesaria alguna observación, se encuentra disponible un campo de texto libre.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image4.png" style="width:5.89306in;height:2.7625in" />

**Tipo valor:** TARJETA

**Tipo:** TARJETA DE CREDITO, TARJETA DE DÉBITO

**Moneda:** pesos o cualquier otra moneda que se encuentre configurada y que será utilizada para realizar cobranzas.

**Paga con:** importe de la cobranza.

**Tarjeta:** emisora del plástico.

**<u>Cobros parciales</u>**

El sistema permite realizar una cobranza de manera parcial y admite 3 posibilidades que se habilitarán en el momento en que el monto pagado sea menor al adeudado:

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image5.png" style="width:5.9in;height:2.45278in" />

- La posibilidad de generar una nota de crédito automática en los casos en los cuales se “Autoriza a no cobrar el parcial”. Tesorería decidirá si se omitirá el cobro de este monto, o si quedará en cuenta corriente. En caso de autorizar los movimientos, se generará una nota de crédito que impactará en la cuenta corriente del paciente, se dispone de un campo de observaciones para completar en caso de ser necesario.

- “Dejar en cuenta corriente” -\> permitir la cobranza y que ese parcial que no fue saldado o abonado quede en la cuenta corriente del paciente como una deuda pendiente, la misma podrá ser cancelada en otro momento.

- “Cancelar” -\> regresar y completar el cobro correspondiente.

  Para los casos en los que sea necesario realizar el cambio de nombre de una persona física o jurídica al momento de emitir una factura, existe un apartado dentro de la pantalla en donde se permite este cambio en los casos en los que quien realiza el pago es una persona/entidad diferente al paciente, por defecto la factura se realizará a nombre del paciente. Independientemente del cambio de nombre/razón social, la cuenta corriente afectada será la del Paciente.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image6.png" style="width:5.90139in;height:2.13611in" />

##### **Ingreso/Egreso de adicionales de caja**

Se corresponden con ingresos y egresos que no están vinculados con los servicios asistenciales.

Los parámetros contables deben estar previamente definidos, serán estos los que habiliten a cada una de las diferentes cajas a realizar cobranzas y pagos sobre determinados conceptos.

Los rangos de autorización estarán previamente definidos, es decir, existe la posibilidad de que solo algunos usuarios tengan permitido realizar este tipo de operaciones, de igual manera, pueden establecerse montos máximos permitidos.

EJ: estacionamiento, viáticos.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image7.png" style="width:5.89444in;height:2.68542in" />

##### **Ingreso/Egreso de fondo de cambio**

Para los casos en que sea necesario ingresar o extraer efectivo de alguna de las cajas se utiliza esta opción -\> tiene por finalidad aumentar o disminuir el saldo de la caja según sea necesario.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image8.png" style="width:6.49653in;height:3.53264in" />

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image9.png" style="width:5.90069in;height:2.75347in" />

**Tipo de comprobante:** INGRESO O EGRESO

**Concepto contable:** previamente definidos, la elección del mismo dependerá del motivo por el cual se realice la operación.

**Concepto:** campo editable para ingresar comentarios

##### **Cobranza en cuenta corriente**

Esta opción se utiliza para los casos en los cuales el paciente tenga una saldos pendientes que se correspondan con una deuda y la misma quiere ser cancelada.

Además, presionando el botón “Cuenta Corriente Paciente” se visualizarán los movimientos que estén afectados en la cuenta corriente del mismo, es decir, sirve de consulta. El sistema brinda la posibilidad de descargar la consulta en Excel o de imprimir la consulta.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image10.png" style="width:6.64167in;height:2.56181in" /><img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image11.png" style="width:5.90347in;height:1.29792in" />

##### **Devolución al paciente de saldo en cuenta corriente**

En los casos en que sea necesario realizar una devolución al cliente se utiliza esta pantalla, aquí se realiza la búsqueda del paciente, se puede visualizar la cuenta corriente del mismo y proceder a la devolución del dinero. Una vez presionado el botón “entregar” se emite una NC por la devolución.

Es importante considerar que no se podrán emitir devoluciones en efectivo por un saldo mayor al que cuente la caja en el momento.

Las devoluciones pueden ser en efectivo, transferencia bancaria, tarjeta de crédito u otros medios de pago que hayan sido habilitados en la configuración de las cajas.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image12.png" style="width:5.65694in;height:2.70833in" />

##### **Cancelación de comprobantes con reintegro**

Esta pestaña se utiliza cuando es necesario realizar una devolución del dinero al paciente. Aquí se listan los comprobantes emitidos por rango de fechas. Al seleccionar el comprobante a cancelar y presionando el botón “cancelar comprobante” el sistema controla que el paciente no haya sido efectivamente atendido y genera la devolución del dinero.

La devolución puede realizarse con efectivo, tarjeta, mediante transferencia bancaria y con cualquier otro medio de pago que esté disponible para operar dentro de la caja. El sistema permite devoluciones que combinen dos o mas formas de pago también.

Generada la devolución, se emite una NC que cancela el comprobante emitido y se elimina al paciente de la cola de espera de atención.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image13.png" style="width:6.32292in;height:3.26875in" />

##### **Cambio tipo de valor**

Permite la modificación de los ingresos/egresos de la caja en caso de error u omisión por parte del cajero. Esta pantalla puede ser parametrizada para el acceso a perfiles que estén autorizados, de no estar un usuario habilitado para realizar modificaciones, deberá pedir autorización al área correspondiente.

Esta opción solo podrá ser utilizada cuando la caja no haya sido cerrada. Aquí se listan los movimientos de la caja, al presionar aquel sobre el que se quiere realizar la modificación, en “Valores a ingresar” se pueden realizar los cambios, esto incluye el monto del ingreso/egreso de caja y el medio por el cual se realizó.

Cualquier cambio/modificación a nivel de operaciones de la caja se da dentro de esta pantalla siempre y cuando la caja no haya sido rendida. Al realizar efectivamente el cambio, se imprime un documento en donde se indican los cambios realizados para su posterior control en caso de corresponder.

Si la caja fue cerrada y se encuentran diferencias por parte del área responsable del control de las mismas, sólo tesorería podrá realizar los cambios correspondientes antes de “autorizar” la caja y que los asientos migren al ERP.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image14.png" style="width:5.90347in;height:3.95625in" />

##### **Consulta movimientos de caja**

Incluye todos los movimientos de la caja, es exportable a Excel y se puede filtrar por rangos de fecha.

Se visualizan los movimientos de una caja, el horario de la operación, el tipo y número de comprobante, el usuario responsable (cajero) y los movimientos de ingreso y egreso separados en columnas.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image15.png" style="width:5.90347in;height:2.61181in" />

##### **Pago adelantado**

La creación de la solicitud de cobranza, que se realiza desde admisión/recepción, habilita al cajero a cobrar al paciente el depósito en garantía hasta concluir trámites administrativos.

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image16.png" style="width:6.83056in;height:2.90069in" />

Dentro de la pantalla se visualizan las solicitudes de pago a cuenta pendientes de cobranza:

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image17.png" style="width:5.90556in;height:0.55556in" />

Para realizar el cobro, se debe seleccionar al paciente y completar campos obligatorios (\*)

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image18.png" style="width:5.90556in;height:2.64375in" />

##### **Rendición de caja**

Se corresponde con el momento en que la caja deba ser rendida, aquí se visualizan todas las operaciones realizadas dentro del turno:

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image19.png" style="width:6.70278in;height:2.79375in" />

-\> Se indican los ingresos y los egresos por tipo de comprobante

-\> Se totalizan los fondos según los diferentes tipos de valores

Al presionar el botón “Rendir” se abre un pop-up que resume los movimientos y la cantidad de dinero a rendir. El cajero deberá contar el efectivo que haya ingresado a la caja y controlar que sea el mismo que le informa el sistema. Si hubiera diferencias también es posible registrarlas e impactarán como “Diferencia de caja”.

El sistema da la posibilidad de dejarle al próximo cajero dinero en efectivo.

Si hubiera alguna diferencia en los saldos el sistema lo indicará y será responsabilidad del cajero rendir la caja generando una “diferencia de caja”. Lo mismo ocurre con el sobrante de caja.<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image20.png" style="width:5.65139in;height:2.66736in" />

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image21.png" style="width:5.90522in;height:3.23611in" />

*Detalle de comprobantes:*

<img src="/mnt/data/.tmp_caja/Caja_Markdown/.media/media/image22.png" style="width:5.90522in;height:1.91667in" />
