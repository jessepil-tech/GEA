Fuente original: BP Laboratorio.docx
Ruta original: Laboratorio/BP Laboratorio.docx
Formato original: DOCX

[ELEMENTO VISUAL NO CONVERTIDO: imagen]

BP – Modelo de Negocios “**Laboratorio**”

Versión 1.0

# Objetivo del documento

En el siguiente documento se describen los procesos de Laboratorio contemplados dentro del módulo de Laboratorio del sistema. El fin del mismo se orienta a que los usuarios:

- conozcan las prestaciones del sistema;

- identifiquen los puntos en común con los procesos que realizan en su operatoria cotidiana;

- determinen cuáles son aquellas tareas que no se encuentran contempladas y deberían ser analizadas en específico.

#  

# Procesos contemplados en el módulo de laboratorio

Mediante el uso del módulo de Laboratorio de TS se podrá estandarizar, sistematizar y controlar los procesos que ocurren desde que un médico solicita un análisis hasta que se obtienen los resultados del mismo y se genera un informe final.

El módulo de Laboratorio permite realizar tareas en las tres fases de trabajo: fase pre-analítica, fase analítica y fase post-analítica. A continuación, se describirán las funcionalidades disponibles en cada una de estas fases.

## Fase pre-analíticaFase pre-analítica

[ELEMENTO VISUAL NO CONVERTIDO: imagen]La fase pre-analítica implica cerca de dos tercios de las tareas realizadas en el laboratorio, pudiendo definir dos subfases: extra e intra laboratorio.

- **Extra-laboratorio**

Comprende:

- **la solicitud del análisis:** esta tarea le compete al área médica y puede realizarse en el ámbito ambulatorio, en la atención de guardia y en internación. La solicitud puede ser de tipo normal o urgente.

<!-- -->

- **La preparación del paciente:** según el ámbito de atención del paciente (ambulatorio o internación) corresponde informar a quien corresponda las condiciones previas al análisis.

- **La toma de muestra:** de manera general, esta tarea se realiza por personal de laboratorio en áreas de internación o de atención de guardia, o en boxes del laboratorio.

**Ámbito ambulatorio**

En caso de que se trate de un paciente ambulatorio, se debe realizar la recepción del paciente, emitiendo la orden de servicio correspondiente y facturando las prácticas si correspondiera. Al momento de llevar a cabo la recepción, se realiza un cuestionario para corroborar que el paciente se encuentre en condiciones para la toma de muestra. El sistema cuenta con la posibilidad de cargar cuestionarios para cada análisis, de manera tal de que el recepcionista acceda rápidamente a las preguntas. Si se realizara más de un análisis, se eliminan las preguntas en común de los cuestionarios, mostrando solo aquellas que no se compartan.

Una vez recepcionado el paciente se le informa en qué fecha estará disponible el informe del análisis.

**Ámbito de internación**

La toma de muestra a pacientes internados puede realizarse a demanda o de manera programada. Para este último caso, existen los trenes de extracción, que permiten agendar en qué momento debe realizarse la toma de muestra a un paciente internado.

Cabe aclarar que, independientemente del ámbito de toma de muestra, el personal puede realizar informes durante el proceso de extractoría.

- **La identificación de la muestra:** esta tarea es fundamental para garantizar la identificación de la muestra y su correcta trazabilidad.

- **La preparación de la muestra para el transporte:** implica acondicionar la muestra para su transporte, teniendo en consideración el tiempo y la forma de transporte.

- **El transporte de la muestra hacia el laboratorio.**

<!-- -->

- **Intra-laboratorio**

Se incluyen en esta subfase:

- **la recepción de muestras en el laboratorio:** las muestras pueden provenir de otro sector de la institución, las puede proporcionar el paciente al momento de recepcionarse o pueden ingresar al laboratorio desde una derivación externa.

En el sistema, existe la posibilidad de recibir muestras adeudadas por el paciente.

- El **check-in** (registro administrativo del ingreso a laboratorio),

- la preparación de la muestra para el análisis;

- el transporte de la muestra al área correspondiente.

## Fase analítica

En esta fase se contemplan todos los procedimientos relacionados con la realización del análisis propiamente dicho y la validación de los resultados.

## 

## [ELEMENTO VISUAL NO CONVERTIDO: imagen]Fase post-analítica

En esta fase se realizan tareas asociadas a:

- la elaboración y el envío del informe al médico solicitante;

- [ELEMENTO VISUAL NO CONVERTIDO: imagen]la interpretación clínica y la toma de decisiones en función de los resultados de los análisis.

#  

# Determinaciones

En el sistema, se deben configurar las determinaciones que correspondan y luego se las asocia a un análisis de laboratorio. Un análisis puede contar con una o más determinaciones.

Algunos de los campos a completar cuando se añade una nueva determinación son:

- código interno de la determinación;

- nombre de la determinación;

- nombre con el cual se visualizará la determinación en las planillas de trabajo.

También existen checkbox donde puede configurarse si la prestación se encuentra activa o no, y si se mostrará una advertencia en caso de presentar un valor crítico.

Una vez que se han configurado estos campos, debe especificarse la **versión de la determinación**, lo cual permite asociar una determinación con un tipo de muestra y una unidad de medida. Aquí también se puede especificar si el resultado de esta determinación se visualizará en el informe y si se imprimirán los valores de referencia junto con el resultado.

Luego, una vez configurada la versión de una determinación, es posible indicar **valores de referencia**. Además del valor propiamente, se debe especificar:

- sexo al que se aplica el valor de referencia;

- rango etario al cual aplica el valor de referencia;

- valores mínimo y máximo de rango de autovalidación;

- valores mínimo y máximo del rango crítico.

Asimismo, existe la posibilidad de configurar delta check para cada versión de prestación, especificando el rango etario de aplicación, la máxima diferencia absoluta, la máxima diferencia porcentual, entre otros.

Por último, para cada determinación puede configurarse un medio de cultivo y equipos de laboratorio asociado a su procesamiento.

#  

# Análisis

Tal como se mencionó anteriormente, un análisis está conformado por una o más prestaciones. Cuando se configura un análisis puede especificarse, entre otros:

- nombre del análisis;

- si se encuentra activo o no;

- si requiere informe;

- el método principal de análisis.

Asimismo, como parte de la configuración de un análisis, se le debe asociar una prestación, indicando en qué período de tiempo (en horas) se puede retirar el informe de la prestación.

En esta sección se pueden especificar las preguntas asociadas a cada análisis, para verificar si el paciente cumple con las condiciones necesarias para realizar el análisis.

#  

# Lugar de procesamiento

El lugar de procesamiento de una muestra depende de su origen y del análisis asociado. En esta sección se debe determinar si las muestras se procesan en el laboratorio que se está configurando, en otro laboratorio de la institución o en un laboratorio externo.

#  

# Microbiología

En relación a esta área de laboratorio, se pueden configurar:

- medios de cultivo;

- microorganismos;

- tipo de antibiótico;

- antibiogramas

# 

# 

#  

# Consideraciones particulares en MEDICUS

De acuerdo a la operatoria propia de los centros ambulatorios y de internación de MEDICUS, se contemplan las siguientes consideraciones.

- Las tareas correspondientes a la subfase extra-laboratorio de la fase pre-analítica serán realizadas en el sistema ThinkSoft, mientras que aquellas pertenecientes a la subfase intra-laboratorio se desarrollarán fuera de ThinkSoft.

- La fase analítica se llevará a cabo fuera de ThinkSoft. Existe la posibilidad de que se realice una última validación en el sistema.

- La fase post-analítica se realizará en ThinkSoft.

- No se contempla recibir derivaciones de un laboratorio externo.

- Se deben desarrollar las integraciones correspondientes para vincular ThinkSoft con el laboratorio externo.
