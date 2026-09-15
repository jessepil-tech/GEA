Fuente original: BP – Control de infecciones.docx
Ruta original: Control de infecciones/BP – Control de infecciones.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/media/image1.png" style="width:2.94792in;height:0.5in" />

**BP – Modelo de Negocios**

**Control de infecciones**

**Versión 1.0**

*Información del Documento*

|  |  |  |
|:---|:---|:---|
|  | **Título del Documento** | BP – Control de infecciones |
| **Información General** | **Localización del documento** |  |

|  |  |  |  |  |
|:---|:---|:---|:---|:---|
| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Ing. Catalina Claucich | Implementación | Análisis Funcional | 01/02/2023 |
|  |  |  |  |  |

|  |  |  |  |  |
|:---|:---|:---|:---|:---|
| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Ing. Silvana Elizondo | Implementación | Líder de proyecto | 06/02/2023 |
|  |  |  |  |  |

|                  |                            |             |         |           |
|:-----------------|:---------------------------|:------------|:--------|:----------|
| **Aprobado por** | **Nombre (Empresa)**       | **Proceso** | **Rol** | **Fecha** |
|                  |                            |             |         |           |
|                  | <span class="mark"></span> |             |         |           |

|               |             |                       |                    |
|:--------------|:------------|:----------------------|:-------------------|
| **Documento** | **Versión** | **Motivo del Cambio** | **Fecha Efectiva** |
| **Historia**  |             |                       |                    |
|               |             |                       |                    |

**Índice de contenido**

[1. Generalidades [6](#generalidades)](#generalidades)

[2. Control infectológico de pacientes internados [6](#control-infectológico-de-pacientes-internados)](#control-infectológico-de-pacientes-internados)

[[Inicio del aislamiento 6](#inicio-del-aislamiento)](#inicio-del-aislamiento)

[Finalización del aislamiento [7](#finalización-del-aislamiento)](#finalización-del-aislamiento)

[3. Consultas [8](#consultas)](#consultas)

**1. Objetivos**

El proyecto de implementación al que este documento pertenece tiene como principales objetivos:

- Integrar, optimizar y automatizar el proceso de Control de infecciones.

- Implementar el módulo de Control de infecciones en el entorno del cliente de una manera eficiente.

- Garantizar el uso correcto por parte del cliente, con relación al módulo de Control de infecciones.

**Objetivo del documento “BP – Procesos de negocio”:**

El presente documento tiene como objetivo principal documentar y *aprobar* todos y cada uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con otros sistemas informáticos implementados en la institución.

La correcta documentación de estos procesos está sujeta a las reuniones continuas con los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir, así como las oportunidades de cambio y mejora. Cada proceso plasmado en este documento debe ser aprobado por el referente designado, y todo aquello que no se registre adecuadamente en esta instancia puede suponer una demora en los tiempos de implementación a futuro.

**2. Alcance**

En el siguiente documento se describirán los procesos que se llevan a cabo dentro del módulo de control de infecciones del sistema Thinksoft HIS. Asimismo, se mencionarán las configuraciones del módulo

1.  Control infectológico de pacientes internados

2.  Consultas

# Generalidades

El módulo de control de infecciones tiene como principal objetivo vigilar, prevenir y controlar infecciones en la institución, reduciendo riesgo de infecciones nosocomiales en pacientes y empleados, optimizando el uso de los recursos a través un programa preventivo.

Se compone de una sección para configuración, el menú de infectología y consultas.

# Control infectológico de pacientes internados

Desde el módulo de control de infecciones es posible visualizar un listado de pacientes internados según sector de internación y determinar si alguno de ellos requiere inicio o finalización de su aislamiento. Los datos que se visualizan son nro internación, función de internación, servicio, ambiente, cama, paciente, tipo de aislamiento, microorganismo y observaciones.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/media/image2.png" style="width:6.26181in;height:0.88681in" />

## Inicio del aislamiento

Al seleccionar un paciente para iniciar su aislamiento, se visualiza su nombre y datos de su internación y se solicita el ingreso de:

- Fecha de inicio del aislamiento.

- Tipo de aislamiento. La institución puede cargar los tipos de aislamientos para adecuarlos a la operatoria.

  <img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/media/image3.png" style="width:6.26875in;height:1.06597in" />

- Microorganismo. Configurable por el usuario.

En cada tipo de aislamiento puede asignarse un color, por lo que cada paciente aislado se verá resaltado con el color correspondiente a su aislamiento. Además, el aislamiento se observará en el censo gráfico, con el color y microorganismo asignado.

Una vez iniciado el aislamiento, en las consultas de housekeeping también se visualizará el paciente resaltado con el color del tipo de aislamiento y se mostrará el microorganismo.

## Finalización del aislamiento

Al seleccionar un paciente para finalizar su aislamiento, se consultará si efectivamente se quiere finalizar, a modo de confirmación. En caso de que la respuesta sea positiva, el aislamiento quedará finalizado.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/media/image4.jpeg" style="width:6.27083in;height:2.31667in" />

# Consultas

El módulo contiene diferentes consultas, que tienen como fin proporcionar información relacionada con aislamientos, dispositivos colocados y alertas de microorganismos.

**Consulta infectología:** permite visualizar el histórico de aislamientos para un sector de internación en un centro de atención y en una fecha concreta.

Se observa en la consulta la función de internación, sector, servicio, ambiente, cama, tipo aislamiento, microorganisno, fecha de inicio de aislamiento, el personal que lo indicó, fecha fin y personal que finalizó.

<img src="/mnt/data/.tmp_infecciones/Control_de_Infecciones_Markdown/.media/media/image5.png" style="width:6.2625in;height:1.1875in" />

**Consulta generador de censo:** esta consulta permite visualizar una foto de las ocupaciones y la disponibilidad de camas. Allí se brinda también información relacionada con el aislamiento de cada paciente. Es posible visualizar todos los pacientes internados o sólo aquellos que están aislados.

**Consulta alertas:** muestra el listado de aislamientos que tuvo o tiene un paciente, en los cuales se asignó un microorganismo que tenía el check “Emite alerta” tildado. Este parámetro se configura en la creación o edición de cada microorganismo.
