Fuente original: BP – Control de infecciones - Configuración.pdf
Ruta original: Control de infecciones/BP – Control de infecciones - Configuración.pdf
Formato original: PDF

BP – Modelo de Negocios


 Control de infecciones -
           Configuración
                    Versión 1.0

 Información del Documento

                Título del Documento           BP – Control de infecciones

Información     Localización             del
General         documento




Preparado por   Nombre (Empresa)                   Gerencia          Rol                  Fecha




                Ing. Catalina Claucich             Implementación    Análisis Funcional   01/02/2023




Revisado por    Nombre (Empresa)                   Gerencia          Rol                  Fecha




                Ing. Silvana Elizondo              Implementación    Líder de proyecto    06/02/2023




Aprobado por    Nombre (Empresa)                        Proceso              Rol          Fecha




Documento
                Versión        Motivo del Cambio                                          Fecha Efectiva


Historia

Índice de contenido


1. Generalidades ........................................................................................................................6
2. Configuración del módulo ......................................................................................................6

1. Objetivos

   El proyecto de implementación al que este documento pertenece tiene como principales
objetivos:

   ➢ Integrar, optimizar y automatizar el proceso de Control de infecciones.
   ➢ Implementar el módulo de Control de infecciones en el entorno del cliente de una
       manera eficiente.
   ➢ Garantizar el uso correcto por parte del cliente, con relación al módulo de Control de
       infecciones.

Objetivo del documento “BP – Procesos de negocio”:

   El presente documento tiene como objetivo principal documentar y aprobar todos y cada
uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en
las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con
otros sistemas informáticos implementados en la institución.

   La correcta documentación de estos procesos está sujeta a las reuniones continuas con
los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para
relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir,
así como las oportunidades de cambio y mejora. Cada proceso plasmado en este
documento debe ser aprobado por el referente designado, y todo aquello que no se registre
adecuadamente en esta instancia puede suponer una demora en los tiempos de
implementación a futuro.

2. Alcance


  En el siguiente documento se describirán las configuraciones del módulo de control de
infecciones del sistema Thinksoft HIS.

   1. Generalidades
El módulo de control de infecciones tiene como principal objetivo vigilar, prevenir y controlar
infecciones en la institución, reduciciendo riesgo de infecciones nosocomiales en pacientes
y empleados, optimizando el uso de los recursos a través un programa preventivo.

Se compone de una sección para configuración, el menú de infectología y consultas.


   2. Configuración del módulo
Los parámetros configurables son microorganismos y tipos de aislamiento.

Microorganismo

En la aplicación pueden definirse diferentes microorganismos, que podrán ser
seleccionados al momento de realizar los diferentes registros relacionados con este módulo.

Para ello, se debe acceder a Control de infecciones » Configuración » Microorganismo:




Allí se definirá el nombre del microorganismo, su número de orden y si emite alerta o no. Si
el check “Emite alerta” se encuentra activado, los aislamientos que tengan el
microorganismo asociado se visualizarán en determinadas consultas y generarán un
registro de advertencia:

Tipo de aislamiento

Es posible especificar los distintos tipos de aislamientos que se contemplarán en la
institución. Para ello se debe ingresar a Control de infecciones » Configuración » Tipo
Aislamiento:




Los parámetros a determinar son: nombre del tipo de aislamiento, si se encuentra activo o
no, y el color que lo representará en el censo gráfico y en la planilla de control de
infecciones.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
