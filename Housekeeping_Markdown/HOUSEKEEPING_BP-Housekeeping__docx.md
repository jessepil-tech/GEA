Fuente original: BP - Housekeeping.docx
Ruta original: Housekeeping/BP - Housekeeping.docx
Formato original: DOCX

<img src="/mnt/data/.tmp_housekeeping/media/media/image1.png" style="width:2.94792in;height:0.5in" />

**BP – Modelo de Negocios**

**Housekeeping**

**Versión 1.0**

#### Información del Documento

|  |  |  |
|----|----|----|
|  | **Título del Documento** | BP – Modelo de Negocios - Housekeeping |
| **Información General** | **Localización del documento** |  |

|  |  |  |  |  |
|:--:|:--:|:--:|:--:|:--:|
| **Preparado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Ing. Nazarena Gareis | Soporte funcional | Analista funcional | 20/10/22 |
|  | Francisco Gallo | Soporte funcional | Analista funcional | 09/02/2023 |
|  | Gaston Palopoli | Soporte funcional | Analista funcional | 10/02/2023 |

|  |  |  |  |  |
|:--:|:--:|:--:|:--:|:--:|
| **Revisado por** | **Nombre (Empresa)** | **Gerencia** | **Rol** | **Fecha** |
|  | Gaston Palopoli | Soporte funcional | Analista funcional | 10/02/2023 |
|  |  |  |  |  |

|                  |                      |             |         |           |
|:----------------:|:--------------------:|:-----------:|:-------:|:---------:|
| **Aprobado por** | **Nombre (Empresa)** | **Proceso** | **Rol** | **Fecha** |
|                  |                      |             |         |           |
|                  |                      |             |         |           |
|                  |                      |             |         |           |
|                  |                      |             |         |           |

|               |             |                       |                    |
|:-------------:|:-----------:|:---------------------:|:------------------:|
| **Documento** | **Versión** | **Motivo del Cambio** | **Fecha Efectiva** |
| **Historia**  |    1.0.0    |                       |                    |
|               |             |                       |                    |
|               |             |                       |                    |
|               |             |                       |                    |
|               |             |                       |                    |

**Índice de contenido**

[Objetivos [5](#objetivos)](#objetivos)

[Alcance del Proyecto [6](#alcance-del-proyecto)](#alcance-del-proyecto)

[Configuración [7](#configuración)](#configuración)

[**Tipo Housekeeping [7](#tipo-housekeeping)**](#tipo-housekeeping)

[Censo Grafico: Eventos Housekeeping [9](#censo-grafico-eventos-housekeeping)](#censo-grafico-eventos-housekeeping)

[**Consulta Housekeeping [10](#consulta-housekeeping)**](#consulta-housekeeping)

[Módulo Housekeeping [10](#módulo-housekeeping)](#módulo-housekeeping)

##### 1. Objetivos

El proyecto de implementación al que este documento pertenece tiene como principales objetivos:

- Integrar, optimizar y automatizar el proceso de alta de camilla o ambiente incluyendo la asignación de Housekeeping desde el módulo de internación junto con su sistema de mensajería.

- Implementar el módulo de Housekeeping en el entorno del cliente, de una manera eficiente.

- Garantizar el uso correcto por parte del cliente, con relación al módulo Housekeeping.

**Objetivo del documento “BP – Procesos de negocio”:**

El presente documento tiene como objetivo principal documentar y *aprobar* todos y cada uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con otros sistemas informáticos implementados en la institución.

La correcta documentación de estos procesos está sujeta a las reuniones continuas con los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir, así como las oportunidades de cambio y mejora. Cada proceso plasmado en este documento debe ser aprobado por el referente designado, y todo aquello que no se registre adecuadamente en esta instancia puede suponer una demora en los tiempos de implementación a futuro.

##### 2. Alcance del Proyecto

Este documento se enfocará en los procesos establecidos para la implementación del módulo Housekeeping:

- Parámetros de configuración (gestión de tipo de Housekeeping y mensajería).

- Solicitud de Housekeeping.

- Inicio y Fin del Housekeeping.

# Configuración

En el sistema Thinksoft, es requisito necesario el poder tener parametrizado ciertos roles que definirán la posibilidad del envió de la solicitud de limpieza hacia el módulo correspondiente.

Para ello, desde la *configuración operativa* del ***Personal***, se debe contar con la habilitación de los siguientes roles:

- Administración Camas

- Liberación Camas

<img src="/mnt/data/.tmp_housekeeping/media/media/image2.png" style="width:6.29931in;height:1.82431in" alt="Interfaz de usuario gráfica, Texto, Aplicación, Correo electrónico Descripción generada automáticamente" />

## Tipo Housekeeping

Con el fin de poder visualizar las solicitudes de limpieza o Housekeeping, se debe parametrizar el tipo Housekeeping el cual define el evento de cómo se disparará la limpieza.

<img src="/mnt/data/.tmp_housekeeping/media/media/image3.png" style="width:6.29931in;height:3.85764in" alt="Interfaz de usuario gráfica, Texto, Aplicación, Correo electrónico Descripción generada automáticamente" />

Para ello es necesario indicarle el centro al cual pertenece el Housekeeping, definirle un nombre al tipo de Housekeeping a generar y el evento que lo contemple. El “***Evento Housekeeping***” indica cuando se dispara la limpieza y son parámetros ya preestablecidos en el sistema Thinksoft. Entre ellos pueden encontrar:

- Alta física (se desocupa la cama del paciente)

- Alta administrativa

- Manual (se solicita el housekeeping manualmente desde el censo gráfico)

- Inhabilitación ambiente

<img src="/mnt/data/.tmp_housekeeping/media/media/image4.png" style="width:6.29931in;height:1.21597in" alt="Interfaz de usuario gráfica, Aplicación Descripción generada automáticamente" />

En cada tipo housekeeping se define si se realizan envíos de aviso por correo o por SMS y el tipo de correo o sms que se enviará según corresponda.

<img src="/mnt/data/.tmp_housekeeping/media/media/image5.png" style="width:6.29931in;height:0.4in" />

<img src="/mnt/data/.tmp_housekeeping/media/media/image6.png" style="width:6.29931in;height:0.37361in" />

Tanto el tipo de correo como el tipo de SMS son parámetros configurables desde Thinksoft en los cuales se deben definir:

- Correo: destinatario, el asunto y el cuerpo de este

- SMS: destinatario y el mensaje.

<img src="/mnt/data/.tmp_housekeeping/media/media/image7.png" style="width:6.29931in;height:2.61458in" alt="Interfaz de usuario gráfica, Texto, Aplicación, Correo electrónico Descripción generada automáticamente" />

# Censo Grafico: Eventos Housekeeping 

En el censo gráfico, donde se muestran las camas de los pacientes, se puede observar si en la cama se ha iniciado la limpieza o si tiene una solicitud pendiente de Housekeeping. Esto ocurre cuando se realiza una solicitud sobre algún evento de Housekeeping.

<img src="/mnt/data/.tmp_housekeeping/media/media/image8.png" style="width:6.29931in;height:0.32986in" />

<img src="/mnt/data/.tmp_housekeeping/media/media/image9.png" style="width:2.73815in;height:1.18538in" alt="Interfaz de usuario gráfica, Aplicación Descripción generada automáticamente" /> <img src="/mnt/data/.tmp_housekeeping/media/media/image10.png" style="width:2.71603in;height:1.18655in" />

Las solicitudes de Housekeeping pueden realizarse mediante un proceso manual, el cual permite seleccionar sobre la cama misma (este ocupada por un paciente o no) el envió de la solicitud de Housekeeping pendiente, la cual permite indicar observaciones al personal de limpieza.

<img src="/mnt/data/.tmp_housekeeping/media/media/image11.png" style="width:1.19375in;height:0.58472in" alt="Interfaz de usuario gráfica, Texto, Aplicación Descripción generada automáticamente" /><img src="/mnt/data/.tmp_housekeeping/media/media/image12.png" style="width:2.70866in;height:1.17717in" alt="Interfaz de usuario gráfica, Aplicación Descripción generada automáticamente" /> <img src="/mnt/data/.tmp_housekeeping/media/media/image13.png" style="width:1.64455in;height:0.80088in" alt="Imagen que contiene Escala de tiempo Descripción generada automáticamente" />

Al liberarse una cama la cual estaba ocupada por un paciente, se da automáticamente el evento “alta física”, lo que inicia automáticamente la solicitud de Housekeeping pendiente.

<img src="/mnt/data/.tmp_housekeeping/media/media/image14.png" style="width:2.70866in;height:1.15354in" alt="Interfaz de usuario gráfica, Texto, Aplicación Descripción generada automáticamente" />

Luego de la finalización sobre la limpieza de la cama, se dejará de ver la referencia de limpieza y mismo la cama pasara a estar Habilitada y Libre para ser ocupada por el próximo paciente.

## Consulta Housekeeping

Existe una consulta de Housekeeping dentro del módulo de *Admisión Internados* el cual permite visualizar todas las solicitudes de Housekeeping pendientes, iniciadas y finalizadas junto con el tiempo total empleado en la limpieza.

En esta consulta se podrá filtrar por fecha, sector internación y por tipo de Housekeeping solicitado.

<img src="/mnt/data/.tmp_housekeeping/media/media/image15.png" style="width:6.29931in;height:0.54444in" />

<img src="/mnt/data/.tmp_housekeeping/media/media/image16.png" style="width:6.29931in;height:0.96458in" alt="Interfaz de usuario gráfica, Aplicación Descripción generada automáticamente" />

# Módulo Housekeeping 

Al querer ingresar al módulo Housekeeping se pedirá seleccionar el centro de atención correspondiente, en caso de que el mismo personal este habilitado en más de un centro.

<img src="/mnt/data/.tmp_housekeeping/media/media/image17.png" style="width:2.70214in;height:1.39766in" alt="Interfaz de usuario gráfica, Aplicación Descripción generada automáticamente" />

Dentro del módulo, se visualizará una planilla con las solicitudes generadas al centro al cual se haya ingresado previamente. La lista contiene la siguiente información:

- Tipo

- Ambiente

- Nro Cama

- Estado cama

- Estado Ambiente

- Fecha solicitud de Housekeeping

- Fecha Inicio de Housekeeping

- Tipo Alta

- Observaciones

Desde esta pantalla se puede iniciar la limpieza correspondiente, la cual deja constancia al comienzo de esta asignándose la fecha y hora actual de inicio.

En caso de que se solicite el Housekeeping sobre un paciente dado de alta es que se puede visualizar el tipo de alta dada y mismo las observaciones descriptas al momento de la solicitud.

Es sumamente necesario y obligatorio poder dar fin a la limpieza al momento de concluir la misma para poder generar la habilitación y liberación de la cama para el próximo paciente.

Las solicitudes también pueden ser anuladas en caso de que se haya realizado una solicitud errónea del ambiente.

<img src="/mnt/data/.tmp_housekeeping/media/media/image18.png" style="width:6.29931in;height:0.48542in" />

<img src="/mnt/data/.tmp_housekeeping/media/media/image19.png" style="width:6.29931in;height:3.33333in" alt="Diagrama Descripción generada automáticamente" />
