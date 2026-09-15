Fuente original: BP Facturación internados.docx
Ruta original: Facturación/BP Facturación internados.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_facturacion/media/media/image1.png" style="width:2.94792in;height:0.5in" />

BP – Modelo de Negocios “**Facturación en internación**”

Versión 1.0

# Objetivo del documento

En el siguiente documento se describen los procesos de facturación de internados contemplados en el sistema de ThinkSoft.

Es importante aclarar que el proceso de facturación está dividido en cuatro etapas, que son detalladas en este documento:

\- revisión de órdenes de servicio;

\- prefacturación;

\- facturación;

\- emisión de lotes de presentación.

**Conceptos importantes**

En primer lugar, es necesario describir dos conceptos fundamentales para abordar el proceso de facturación.

- **Prestación pactada o convenida.** Una prestación se encuentra pactada o convenida si se cumplen las siguientes condiciones:

  \- se encuentra definida en un grupo de prestaciones pactadas para el convenio;

  \- se encuentra definida en el ámbito de realización (internado, ambulatorio o ambos);

  \- debe poder obtenerse un precio;

  \- debe poder obtenerse, a partir del convenio, el tipo de IVA, la razón social y la condición de IVA de la entidad asociada.

- **Coseguro.** Se denomina así a todo importe que no está cubierto por un convenio y debe ser abonado por el paciente. Aquí no se realiza distinción entre coseguro y copago.

  **Proceso de facturación**

A continuación se abordan los procesos claves asociados al circuito de facturación a pacientes internados.

**Admisión del paciente**

En primer lugar, se contempla la admisión del paciente en la institución. Para esto debe seguirse el proceso descripto en el documento correspondiente a este circuito.

Un documento fundamental en este proceso de admisión es la orden de internación. En caso de que el paciente o su responsable la provean, el personal de admisión la ingresa en el sistema. Si el paciente se presentara sin la orden de internación, todas las órdenes de servicio de internados que se generen serán valorizadas a valores del convenio correspondiente, pero establecidas a cargo del paciente, es decir, con cobertura nula, hasta que el paciente presente la orden de internación.

Asimismo, dependiendo de las configuraciones establecidas para el convenio y patología al realizar la admisión se determina cuál es el monto a abonar por paciente (o su responsable) en concepto de garantía, según corresponda. Una vez realizado este pago, la admisión queda confirmada.

A partir de este momento, es posible emitir órdenes de servicio de internado para realizar prácticas o administrar medicamentos a ese paciente.

**Importante:**

- la orden de internación redefine precios y coberturas establecidas por el convenio, pudiendo fijar adicionalmente montos con topes de día o de internación para prácticas o medicamentos.

- La orden de internación puede cargarse desde Admisión o desde Facturación de internados en cualquier momento.

**Emisión de órdenes de servicio**

El personal de cada servicio en el cual se realiza una atención sobre un paciente internado debe registrar tanto las prestaciones realizadas, como los ítems y adicionales que se consumieron. Este registro se visualiza en una orden de servicio (OS), que automáticamente se refleja en el área de facturación de internados.

La carga realizada en los servicios debe llevarse a cabo como si al paciente se le facturara cada prestación individual; corresponde al área de facturación determinar si es apropiado modular o variar la forma de facturación. Es por esto que las OS de internados se valorizan por prestación al momento de ser generadas.

Tal como se mencionó anteriormente, si el paciente no tiene orden de internación, los valores se calculan según el convenio y se estipulan a cargo del paciente, hasta que se presente la orden de internación.

Es así, que la cobertura del paciente durante su internación queda determinada por la orden de internación. El cálculo de los valores de prestaciones e ítems es automático, y se realiza tal como se detalla a continuación.

- **Cálculo de valores de prestaciones**

Considerando el siguiente orden, el sistema trata de encontrar un precio de la prestación pactado según:

1\) el profesional en el servicio donde se realiza la práctica;

2\) el servicio que realiza la práctica;

3\) el plan del paciente;

4\) el grupo de prestaciones pactadas para el convenio.

En caso de que no se encuentre un valor pactado para el primer nivel, se continúa con el segundo, y así sucesivamente.

Si la prestación no está pactada en el convenio, se consideran el valor y la cobertura determinadas por el convenio por defecto. De manera general, se setea como convenio por defecto el convenio particular o privado, el cual debe incluir todas las prácticas que se realizan en cada servicio. Suponiendo que una práctica no se encuentre convenida en el convenio por defecto no se permite su realización, ya que no es posible determinar el valor de facturación.

- **Cálculo de valores de ítems**

En este caso el sistema se fija si existen valores particulares pactados según el siguiente orden:

1\) por ítem para el convenio;

2\) por definición de precio de venta. El precio de venta para los medicamentos puede ser fijado por alfabeta o por un markup a partir del precio de compra según el tipo de ítem y el rango de precio de compra.

Los valores de venta de descartables se definen a partir de un markup, de acuerdo al precio de compra por tipo de ítem y rango de precio de compra.

**Importante:** existe la posibilidad de marcar determinados ítems de farmacia como exentos de IVA. Esto puede suceder, por ejemplo, con medicación oncológica o de HIV.

**Cálculo de IVA**

A continuación se detalla el procedimiento que se realiza para calcular el IVA a pacientes particulares y convenio/coseguro.

- **Cálculo al facturar a pacientes particulares**

Suponiendo un valor de prestación de \$10, el cálculo correspondiente se detalla en la siguiente tabla:

<table>
<colgroup>
<col style="width: 39%" />
<col style="width: 45%" />
<col style="width: 15%" />
</colgroup>
<thead>
<tr>
<th style="text-align: center;">Condición de IVA</th>
<th style="text-align: center;">Calculo</th>
<th style="text-align: center;">Letra Factura</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: left;"><p>CF (Consumidor final)</p>
<p>EX (Exento)</p></td>
<td style="text-align: left;">10 * 1.21 = 12.10</td>
<td style="text-align: center;">B</td>
</tr>
<tr>
<td style="text-align: left;">RI (Responsable Inscripto)</td>
<td style="text-align: left;">10 * 1.21 = 12.10</td>
<td style="text-align: center;">A</td>
</tr>
<tr>
<td style="text-align: left;">NC (No categorizado)</td>
<td style="text-align: left;">(10 * 1.21) * 1.135 = 13.73</td>
<td style="text-align: center;">B</td>
</tr>
<tr>
<td style="text-align: left;">RM (Responsable Monotributo)</td>
<td style="text-align: left;">10 * 1.21 = 12.10</td>
<td style="text-align: center;">B</td>
</tr>
<tr>
<td style="text-align: left;">RN (Responsable No Inscripto.)</td>
<td style="text-align: left;">(10 * 1.21) + (10 * 0.105) = 13.15</td>
<td style="text-align: center;">A</td>
</tr>
</tbody>
</table>

- **Cálculo al facturar a convenio/coseguro**

Suponiendo también un valor de prestación de \$10, el cálculo correspondiente sería el siguiente:

<table>
<colgroup>
<col style="width: 39%" />
<col style="width: 0%" />
<col style="width: 45%" />
<col style="width: 0%" />
<col style="width: 15%" />
</colgroup>
<thead>
<tr>
<th style="text-align: center;">Condición de IVA</th>
<th colspan="2" style="text-align: center;">Calculo</th>
<th colspan="2" style="text-align: center;">Letra Factura</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: left;"><p>Paciente Voluntario</p>
<p>CF (Consumidor final)</p>
<p>EX (Exento)</p></td>
<td colspan="2" style="text-align: left;">10 * 1.105 = 11.50</td>
<td colspan="2" style="text-align: center;">B</td>
</tr>
<tr>
<td style="text-align: left;"><p>Paciente Obligatorio</p>
<p>CF (Consumidor final)</p>
<p>EX (Exento)</p></td>
<td colspan="2" style="text-align: left;">10</td>
<td colspan="2" style="text-align: center;">B</td>
</tr>
<tr>
<td style="text-align: left;"><p>Paciente Voluntario</p>
<p>RI (Responsable Inscripto)</p></td>
<td colspan="2" style="text-align: left;">10 * 1.105 = 11.50</td>
<td colspan="2" style="text-align: center;">A</td>
</tr>
<tr>
<td style="text-align: left;"><p>Paciente Obligatorio</p>
<p>RI (Responsable Inscripto)</p></td>
<td colspan="2" style="text-align: left;">10</td>
<td colspan="2" style="text-align: center;">A</td>
</tr>
<tr>
<td style="text-align: left;"><p>Paciente Voluntario</p>
<p>NC (No categorizado)</p></td>
<td colspan="2" style="text-align: left;">(10 * 1.105) * 1.135 = 12.54</td>
<td colspan="2" style="text-align: center;">B</td>
</tr>
<tr>
<td colspan="2" style="text-align: left;"><p>Paciente Obligatorio</p>
<p>NC (No categorizado)</p></td>
<td colspan="2" style="text-align: left;">10</td>
<td style="text-align: center;">B</td>
</tr>
<tr>
<td colspan="2" style="text-align: left;"><p>Paciente Voluntario</p>
<p>RM (Responsable Monotributo)</p></td>
<td colspan="2" style="text-align: left;">10 * 1.105 = 11.50</td>
<td style="text-align: center;">B</td>
</tr>
<tr>
<td colspan="2" style="text-align: left;"><p>Paciente Obligatorio</p>
<p>RM (Responsable Monotributo)</p></td>
<td colspan="2" style="text-align: left;">10</td>
<td style="text-align: center;">B</td>
</tr>
<tr>
<td colspan="2" style="text-align: left;"><p>Paciente Voluntario</p>
<p>RN (Responsable No Inscripto)</p></td>
<td colspan="2" style="text-align: left;">(10 * 1.105) + ( 10 * 0.0525) = 11.58</td>
<td style="text-align: center;">A</td>
</tr>
<tr>
<td colspan="2" style="text-align: left;"><p>Paciente Obligatorio</p>
<p>RN (Responsable No Inscripto)</p></td>
<td colspan="2" style="text-align: left;">10</td>
<td style="text-align: center;">A</td>
</tr>
</tbody>
</table>

**Revisión de órdenes de servicio de internación, órdenes de internación y prórrogas.**

Como ya se mencionó, cada servicio emite la o las órdenes de servicio de internados correspondientes a los actos médicos que se realicen al paciente.

Asimismo, por cada tipo de sector en el cual se encuentra internado el paciente, se registran las pensiones y derechos determinados para cada tipo de cama ocupada.

Es importante saber que un facturista debe chequear diariamente aquellos pacientes sin órdenes de internación o que requieran prórroga. En el sistema, estos pacientes se visualizan en rojo en la lista de pacientes internados.

**Prefacturación**

Una vez que las órdenes de internación han sido cargadas (por admisión o facturación de internados), el facturista puede proceder a prefacturar la internación.

El proceso de prefacturación es un mecanismo óptimo para realizar auditorías de pacientes internados in situ con auditores externos. Al verificar la correcta prefacturación del paciente, el auditor externo firma la pre-factura, para luego poder emitir la factura definitiva.

Durante el proceso de prefacturación, se puede añadir prestaciones/ítems que no se hayan registrado durante la internación y definir la utilización de los módulos. Los mismos pueden ser:

\- módulo día;

\- módulo patología;

\- módulo manual.

**Facturación**

A partir del momento en que el médico del servicio realiza el alta médica del paciente, queda inhibida la carga de órdenes de servicio al paciente, lo cual posibilita la emisión de las facturas definitivas.

Todas las facturas emitidas ingresan en la cuenta corriente del paciente o del convenio, según corresponda. El importe de la factura se calcula en base a las órdenes de servicio emitidas a un paciente durante la internación.

La facturación se realiza por convenios, emitiendo el comprobante fiscal a la razón social estipulada por la entidad asociada el convenio en cuestión. En el caso en que el convenio sea particular o privado, se emite la factura a nombre del paciente o del responsable de facturación, según corresponda. Asimismo, cabe destacar que las cuentas corrientes se clasifican por convenio, pudiendo ser agrupadas por entidades.

**Importante:**

- Al emitir la factura se genera el comprobante fiscal.

- No es posible emitir una factura a un paciente y/o convenio, si no se ha especificado el convenio y plan.

Cuando se ha emitido la factura, existen un camino posible para volver atrás dicha facturación:

- cancelar la factura con emisión de nota de crédito. En este caso, la factura emitida sigue siendo un comprobante válido, pero se emite una nota de crédito cancelando en el importe total o parcial de la factura. Las prestaciones o ítems cancelados regresan al estado de prefacturación para ser tratados nuevamente.

**Presentación de lotes de facturación a entidades**

Luego de haber emitido las facturas de internación, el facturista define el lote de presentación, para lo cual debe seleccionar del listado de facturas emitidas y no presentadas aquellas facturas a presentar. Una vez determinado esto, el sistema se encuentra en condiciones de generar un documento con dicha presentación (medio magnético) e imprimir los master que acompañan a las facturas presentadas.
