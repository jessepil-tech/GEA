Fuente original: Configuración Enfermeria internados.pdf
Ruta original: Enfermería Internados/Configuración Enfermeria internados.pdf
Formato original: PDF

BP – Modelo de Negocios

Enfermería    internados   -
Configuración

Versión 1.0

                                   BP-Modelo de Negocios-                      Página 2 de 11
                                    Enfermería Internados



            Información del Documento

                Título del Documento       BP – Modelo de Negocios - Enfermería internados

Información     Localización del
General         documento




Preparado por   Nombre (Empresa)               Gerencia         Rol                   Fecha




                Ing. Silvana Elizondo          Implementación   Análisis funcional    13/02/23




Revisado por    Nombre (Empresa)               Gerencia         Rol                   Fecha




                Ing. Catalina Claucich         Implementación   Coordinadora          13/02/23




Aprobado por    Nombre (Empresa)                    Proceso             Rol           Fecha




Documento       Versión        Motivo del Cambio                                      Fecha Efectiva


Historia        1.0.0

                                         BP-Modelo de Negocios-                                          Página 3 de 11
                                          Enfermería Internados




Configuración Enfermeria internados ...................................................... 6

  Formulario de examen físico .................................................................................................. 9

  Turnos de enfermeria ........................................................................................................... 10

  Dominios de enfermería ......................................................................................................... 6

  Repedido items internacion .................................................................................................. 11

                               BP-Modelo de Negocios-                        Página 4 de 11
                                Enfermería Internados




             1. Objetivos
   El proyecto de implementación al que este documento pertenece tiene como principales
objetivos:

   ●   Determinar las ventajas operativas del uso del módulo Enfermería internados y su
       configuración.

   ●   Implementar el módulo de Enfermería internados en el entorno del cliente, de una
       manera eficiente.


   ●   Promover el uso correcto por parte del cliente.


Objetivo del documento “BP – Procesos de negocio”:

   El presente documento tiene como objetivo principal documentar y aprobar todos y cada
uno de los procesos que se llevarán a cabo –haciendo uso del sistema al implementar– en
las áreas incluidas en el alcance de este proyecto, así como las interfaces necesarias con
otros sistemas informáticos implementados en la institución.

   La correcta documentación de estos procesos está sujeta a las reuniones continuas con
los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para
relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir, así
como las oportunidades de cambio y mejora. Cada proceso plasmado en este documento
debe ser aprobado por el referente designado, y todo aquello que no se registre
adecuadamente en esta instancia puede suponer una demora en los tiempos de
implementación a futuro.

                           BP-Modelo de Negocios-                    Página 5 de 11
                            Enfermería Internados



         2. Alcance del Proyecto
  Este documento se enfocará en los procesos establecidos para la implementación del
módulo Enfermería internados y su configuración.

                             BP-Modelo de Negocios-                       Página 6 de 11
                              Enfermería Internados



Configuración Enfermeria internados

           Dominios de enfermería

                  Control de enfermería
Administración general > Dominios enfermería > Control de enfermería

Allí se crean los controles indicando nombre, nro de orden, si está activo o no, requiere
frecuencia en la indicación, si es ambulatorio o para internados, y si tuviese una prestación
asociada.




                  Tipo dispositivo
Administración general > Dominios enfermería > Tipo dispositivo

En primer lugar deben crearse para luego realizar la asociación de dispositivos por tipo de
enfermería. Aquí puede asignarse un nombre, color, código identificatorio, estado (activo o
no), modificación o eliminación.

                            BP-Modelo de Negocios-                      Página 7 de 11
                             Enfermería Internados



                  Tipo de enfermería
Administración general > Dominios enfermería > Tipo enfermería

Para la creación se debe cargar un nombre que será el tipo de enfermería, ejemplo “SM -
UTI”, “ENFERMERÍA UTI”, entre otros.




En caso que se desee agregar algún navegador, debe indicarse nombre y nro orden.




Finalmente, por cada navegador se requiere asociar los formularios de enfermería.

                             BP-Modelo de Negocios-                     Página 8 de 11
                              Enfermería Internados




Una vez que se crea el tipo de enfermería, se debe asociar al servicio centro.

Administración general > Config operativa > Servicio centro > Dominios de atención >
Parámetros de atención. Sección enfermería > Tipo enfermería internados

A su vez, el parámetro Cantidad de horas evaluación internados, permite establecer cada
cuando tiempo debe completarse una evaluación, pasado el cual la evolución se verá en un
color rojo desde Enfermería internados.

                              BP-Modelo de Negocios-                       Página 9 de 11
                               Enfermería Internados



Posteriormente, se visualiza en el servicio centro correspondiente de enfermería:




           Formulario de examen físico
Administración general > Dominios médicos > Formularios médicos

Para la creación, verificar tipo de formulario Examen físico. El nombre de formulario es
configurable por el usuario, a diferencia del Tipo de formulario que corresponde a un listado
pre determinado.

                                 BP-Modelo de Negocios-                   Página 10 de 11
                                  Enfermería Internados




En la creación de formulario, se permite agregar un detalle o bien realizar una vista previa.




Asociación al servicio centro:

Administración general > Config operativa > Servicio centro > Dominios de atención >
Parámetros de atención

En este menú deben asociarse los formularios de examen físico, al servicio centro. Estos si
bien son visibles en Internación, también se pueden cargar desde enfermería.




           Turnos de enfermeria
Administración general > Config operativa > Servicio centro > Dominios de atención >
Parámetros de atención

Desde allí se configuran el inicio de turno internados, considerando una duración fija de 8 hs.

                            BP-Modelo de Negocios-                    Página 11 de 11
                             Enfermería Internados




          Repedido items internacion
Para cargar un motivo de repedido de ítems desde internación se debe ingresar
Administración general > Configuración general > Motivo




NOTA: La configuración correspondiente a los ítems genéricos y movimientos de stock, se
trató en el módulo de Depósito.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
