Fuente original: Configuración Enfermeria internados.docx
Ruta original: Enfermería Internados/Configuración Enfermeria internados.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image1.png" style="width:2.94792in;height:0.5in" />

**BP – Modelo de Negocios**

**Enfermería internados - Configuración**

**Versión 1.0**

#### Información del Documento

|  |  |  |
|:---|:---|:---|
|  | **Título del Documento** | BP – Modelo de Negocios - Enfermería internados |
| **Información General** | **Localización del documento** |  |

|  |  |  |  |  |
|:---|:---|:---|:---|:---|
| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Ing. Silvana Elizondo | Implementación | Análisis funcional | 13/02/23 |
|  |  |  |  |  |

|  |  |  |  |  |
|:---|:---|:---|:---|:---|
| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Ing. Catalina Claucich | Implementación | Coordinadora | 13/02/23 |
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
| **Historia**  | 1.0.0       |                       |                    |

[Configuración Enfermeria internados [5](#_Toc9957)](#_Toc9957)

[Formulario de examen físico [5](#formulario-de-examen-físico)](#formulario-de-examen-físico)

[Turnos de enfermeria [5](#turnos-de-enfermeria)](#turnos-de-enfermeria)

[Dominios de enfermería [6](#dominios-de-enfermería)](#dominios-de-enfermería)

[Repedido items internacion [9](#repedido-items-internacion)](#repedido-items-internacion)

##### 1. Objetivos

El proyecto de implementación al que este documento pertenece tiene como principales objetivos:

- Determinar las ventajas operativas del uso del módulo Enfermería internados y su configuración.

- Implementar el módulo de Enfermería internados en el entorno del cliente, de una manera eficiente.

- Promover el uso correcto por parte del cliente.

**Objetivo del documento “BP – Procesos de negocio”:**

El presente documento tiene como objetivo principal documentar y *aprobar* todos y cada uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con otros sistemas informáticos implementados en la institución.

La correcta documentación de estos procesos está sujeta a las reuniones continuas con los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir, así como las oportunidades de cambio y mejora. Cada proceso plasmado en este documento debe ser aprobado por el referente designado, y todo aquello que no se registre adecuadamente en esta instancia puede suponer una demora en los tiempos de implementación a futuro.

##### 2. Alcance del Proyecto

Este documento se enfocará en los procesos establecidos para la implementación del módulo Enfermería internados y su configuración.

<span id="_Toc9957" class="anchor"></span>**Configuración Enfermeria internados**

## Dominios de enfermería 

### Control de enfermería 

Administración general \> Dominios enfermería \> Control de enfermería

Allí se crean los controles indicando nombre, nro de orden, si está activo o no, requiere frecuencia en la indicación, si es ambulatorio o para internados, y si tuviese una prestación asociada.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image2.png" style="width:5.53194in;height:2.26181in" />

### Tipo dispositivo

Administración general \> Dominios enfermería \> Tipo dispositivo

En primer lugar deben crearse para luego realizar la asociación de dispositivos por tipo de enfermería. Aquí puede asignarse un nombre, color, código identificatorio, estado (activo o no), modificación o eliminación.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image3.png" style="width:5.76042in;height:1.96181in" />

### Tipo de enfermería

Administración general \> Dominios enfermería \> Tipo enfermería

Para la creación se debe cargar un nombre que será el tipo de enfermería, ejemplo “SM - UTI”, “ENFERMERÍA UTI”, entre otros.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image4.png" style="width:6.29583in;height:1.94236in" />

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image5.png" style="width:6.29097in;height:1.37639in" />

En caso que se desee agregar algún navegador, debe indicarse nombre y nro orden.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image6.png" style="width:4.48958in;height:1.46875in" />

Finalmente, por cada navegador se requiere asociar los formularios de enfermería.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image7.png" style="width:6.29514in;height:1.17569in" />

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image8.png" style="width:6.29028in;height:1.26181in" />

Una vez que se crea el **tipo de enfermería, se debe asociar al servicio centro.**

Administración general \> Config operativa \> Servicio centro \> Dominios de atención \> Parámetros de atención. Sección enfermería \> Tipo enfermería internados

A su vez, el parámetro Cantidad de horas evaluación internados, permite establecer cada cuando tiempo debe completarse una evaluación, pasado el cual la evolución se verá en un color rojo desde Enfermería internados.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image9.png" style="width:2.75in;height:0.82292in" />

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image10.png" style="width:6.29444in;height:2.79861in" />

Posteriormente, se visualiza en el servicio centro correspondiente de enfermería:

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image11.png" style="width:2.71944in;height:3.52778in" />

## Formulario de examen físico

Administración general \> Dominios médicos \> Formularios médicos

Para la creación, verificar tipo de formulario Examen físico. El nombre de formulario es configurable por el usuario, a diferencia del Tipo de formulario que corresponde a un listado pre determinado.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image12.png" style="width:6.29097in;height:2.45486in" />

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image13.png" style="width:6.29931in;height:1.85833in" />

En la creación de formulario, se permite agregar un detalle o bien realizar una vista previa.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image14.png" style="width:2.88542in;height:0.48958in" />

Asociación al servicio centro:

Administración general \> Config operativa \> Servicio centro \> Dominios de atención \> Parámetros de atención

En este menú deben asociarse los formularios de examen físico, al servicio centro. Estos si bien son visibles en Internación, también se pueden cargar desde enfermería.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image15.png" style="width:5.75833in;height:2.3375in" />

## Turnos de enfermeria

Administración general \> Config operativa \> Servicio centro \> Dominios de atención \> Parámetros de atención

Desde allí se configuran el inicio de turno internados, considerando una duración fija de 8 hs.

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image16.png" style="width:6.29514in;height:2.19792in" />

## Repedido items internacion

Para cargar un motivo de repedido de ítems desde internación se debe ingresar Administración general \> Configuración general \> Motivo

<img src="/mnt/data/.tmp_enfermeria/Enfermeria_Internados_Markdown/.media/media/image17.png" style="width:5.76597in;height:3.51875in" />

**NOTA:** La configuración correspondiente a los ítems genéricos y movimientos de stock, se trató en el módulo de Depósito.
