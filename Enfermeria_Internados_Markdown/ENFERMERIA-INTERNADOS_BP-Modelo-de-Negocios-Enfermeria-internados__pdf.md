Fuente original: BP_Modelo de Negocios_Enfermería internados.pdf
Ruta original: Enfermería Internados/BP_Modelo de Negocios_Enfermería internados.pdf
Formato original: PDF

BP – Modelo de Negocios

Enfermería internados

Versión 1.0.2

                                   BP-Modelo de Negocios-                          Página 2 de 20
                                    Enfermería Internados



            Información del Documento

                Título del Documento         BP – Modelo de Negocios - Enfermería internados

Información     Localización del
General         documento




Preparado por   Nombre (Empresa)                 Gerencia           Rol                   Fecha




                Ing. Silvana Elizondo            Implementación     Análisis funcional    13/01/23




Revisado por    Nombre (Empresa)                 Gerencia          Rol                    Fecha




                Ing. Catalina Claucich           Implementación    Coordinadora           01/02/23




Aprobado por    Nombre (Empresa)                      Proceso               Rol           Fecha




Documento       Versión        Motivo del Cambio                                          Fecha Efectiva


Historia        1.0.1          Mejoras   en:    Generalidades,    Administración   según 10/04/23
                               indicación.

                1.0.2          Se agrega descripción de pedido manual reservado a 12/04/23

  BP-Modelo de Negocios-                       Página 3 de 20
   Enfermería Internados



paciente.

Se agrega apartado “Documentación”.

Se agrega diagrama correspondiente a paciente neonato
o pediátrico en loc de dispositivos.

                                            BP-Modelo de Negocios-                                               Página 4 de 20
                                             Enfermería Internados




Generalidades ..........................................................................................7

Evaluación de enfermería ........................................................................ 8

  Resumen de HC ..................................................................................................................... 8

  Examen físico ....................................................................................................................... 10

  Balance Hídrico .....................................................................................................................10

  Colocación de dispositivos ................................................................................................... 10

  Monitoreos ............................................................................................................................ 11

  Scores ................................................................................................................................... 11

  Evolución y cierre de turnos ................................................................................................. 12

  Documentación ..................................................................................................................... 13

Registro de administración y controles ................................................. 13

  Generalidades .......................................................................................................................13

  Administración con indicación .............................................................................................. 14

  Administración directa .......................................................................................................... 16

  Control de enfermería ...........................................................................................................16

  Administración continua ........................................................................................................16

Gestión de depósito ............................................................................... 17

  Pedidos manual .................................................................................................................... 17

  Devolución de medicación reservada a paciente ................................................................ 17

  Ajustes .................................................................................................................................. 19

Consultas ...............................................................................................19

  Consulta de movimientos de stock .......................................................................................19

  Stock depósito ...................................................................................................................... 20

  Stock Item ............................................................................................................................. 20

  Consulta ítem por vencer ..................................................................................................... 20

                               BP-Modelo de Negocios-                        Página 5 de 20
                                Enfermería Internados




             1. Objetivos
   El proyecto de implementación al que este documento pertenece tiene como principales
objetivos:


   ●   Determinar las ventajas operativas del uso del módulo Enfermería internados.


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

                            BP-Modelo de Negocios-                Página 6 de 20
                             Enfermería Internados




         2. Alcance del Proyecto
  Este documento se enfocará en los procesos establecidos para la implementación del
módulo Enfermería internados.

     ●   Evaluación de enfermería
     ●   Registro en planilla de enfermería
     ●   Gestión de depósito
     ●   Consultas de stock

                              BP-Modelo de Negocios-                       Página 7 de 20
                               Enfermería Internados



    Generalidades
Al momento de ingresar al módulo, el personal de enfermería debe seleccionar el sector de
internación en la que realizará su asistencia a pacientes. Al ser una pantalla comunitaria, se
consulta si se desea continuar logueado con el usuario actual o cambiar a otro personal
referente.

El listado de pacientes se agrupa según sector servicio por ejemplo, en el 3 piso se
encuentra Internación general, Ginecología y obstetricia (se verán separados por servicio).
Es posible filtrar dicho listado por paciente, servicio o enfermera asignada (si tuviese).

Los detalles por paciente son Apellido, nombre, edad, convenio, ambiente , número de cama,
enfermero asignado y alertas visuales en casos que corresponda ( ejemplo alergias,
aislamientos, indicaciones por vencer, etc).




Desde el listado de pacientes se pueden realizar distintas operaciones, entre ellos:

   Asignar una/un enfermera/o al paciente

   Mostrar histórico de asignación de enfermería

   Visualización de censo gráfico

   Realizar una evaluación de enfermería

   Ingresar al registro de planilla de enfermería

   Ingresar a un depósito

La evolución de enfermería internados se caracteriza por contar con un menú de navegación
que es customizable según el tipo de enfermería, por ejemplo Enfermería de Guardia o
Enfermería Pediátrica, pueden contar con distintos navegadores (uno contar con balance
hídrico, scores y monitoreos, el otro sólo balance hídrico y monitoreo.

                             BP-Modelo de Negocios-                      Página 8 de 20
                              Enfermería Internados



El nombre del navegador es configurable por el usuario y se le deben asociar los formularios
que se cargarán.

Ejemplo, tipo de enfermería SM - UTI, Navegadores : UTI Balance hídrico, Tipo de
Formulario asociado: Balance Hídrico, Formulario balance hídrico: Balance UTI/UCO.




      Los tipos de formularios son fijos, y corresponden a

   Balance hídrico,

   Formulario HC,

   Localización de dispositivos,

   Monitoreo,

   Score

Es necesario destacar que puede haber más de un formulario configurado, por tipo de
formulario.




    Evaluación de enfermería

            Resumen de HC
El resumen de HC muestra en una sola pantalla toda la información correspondiente a la HC
del paciente seleccionado. En la misma se pueden visualizar y/o agregar:

     Problemas crónicos: El usuario puede visualizar, agregar o eliminar problemas
    crónicos que posea el paciente.

                             BP-Modelo de Negocios-                        Página 9 de 20
                              Enfermería Internados



     Medicación crónica: El usuario puede visualizar, agregar o eliminar medicación
    crónica que tome el paciente.

     Advertencias Paciente: El usuario puede visualizar, agregar o eliminar advertencias
    sobre la salud del paciente.

     Estudios laboratorio: El usuario puede visualizar el resultado de los estudios de
    laboratorio realizados al paciente en el centro de atención.

     Otros estudios: El usuario puede visualizar el resultado de los estudios realizados al
    paciente en el centro de atención (Imágenes, otros servicios con informes).

     Alergias pacientes: El usuario puede visualizar y agregar si el paciente es alérgico.

     Resumen de atenciones: El usuario puede visualizar las atenciones que ha recibido el
    paciente dentro del centro de atención, tanto en ambulatorios como en internados. A su
    vez, puede visualizar la evolución o la epicrisis de un evento de internación anterior.




                   Problemas crónicos
Se pueden agregar Problemas e identificarlos como Crónico o agudo, indicar la fecha de
inicio y si se encuentra activo. Además se pueden cargar observaciones si se considera
necesario. Los problemas se visualizan en la Historia clínica.

El listado de problemas corresponde a la codificación CIE-10.

                  Medicación crónica
Se puede cargar la medicación crónica consumida por el paciente indicando monodroga,
dosis, unidad de potencia, vía de administración y fecha de pescripción.

                  Alergias y advertencias
Desde el “Resumen de HC” existe la posibilidad de agregar alergias y advertencias.

                             BP-Modelo de Negocios-                     Página 10 de 20
                              Enfermería Internados



Estas se mostrarán en un color distintivo (ROJO) y serán visibles por todo el personal de
atención.

Cuando un usuario ingresa a la HC de un paciente que cuenta con Advertencias o Alergias,
el sistema muestra una ventana emergente con las mismas.

En el caso de las Alergias, contienen un control de vigilancia. Esto hace referencia a que
una vez que el paciente es diagnosticado con una alergia, la/s droga/s que se encuentra
afectadas generan un alerta en los procesos de Indicación de Medicación (Médicos),
Preparación de Medicación (Farmacia) y Administración de Medicación (Enfermería),
notificando a cada uno de ellos que existe una contra indicación, independientemente de no
restringir con la continuidad de cada proceso.

           Examen físico
Para el registro de examen físico, además de contener un apartado donde pueden
ingresarse de forma estructurada datos relevantes de los Signos Vitales y Antropometría.
Además, se pueden completar aquellos formularios de historia clínica definidos en el servicio
centro, por ejemplo, para Internación general o Unidad de terapia intensiva.

Los apartados son visibles para todos los actores que intervienen en la evaluación del
paciente (médicos y enfermeros) y llevan un histórico de registros durante la atención. Esto
es, todo lo que se cargue desde Enfermería internados, será visible desde la internación y
visceversa.

           Balance Hídrico
El balance hídrico realiza cálculos automáticos entre los ingresos/egresos, indicando los
acumulados parciales y totales de todos los registros.

Cada registro genera una nueva columna en el balance, donde se visualizarán según el tipo
de formulario de balance especificados, los acumulados por evento (cada registro realizado)
o por hora (suma de todos los registros ingresados por hora).

Se dispone de una herramienta gráfica que muestra las diferentes tendencias resultantes de
todos los registros realizados durante el evento de internación del paciente. Puede
graficarse con líneas o barras, y permite filtrar la visualización a uno o todos los
ingresos/egresos generados.

Puede solicitarse la visualización del acumulado histórico en pantalla como también el
manejo en la creación nuevos detalles de ingreso/egreso es decir, no incluidos en el
formulario de balance inicial, y ocultar los mismos en los casos que no sean relevantes para
su evaluación.

           Colocación de dispositivos
Consiste en la indicación gráfica de los dispositivos colocados a un paciente internado.
Estos pueden estar diferenciados por código de identificación y color respectivo. El esquema
varía si el paciente es adulto/pediátrico o neonato. Se observa un resumen de la fecha de

                               BP-Modelo de Negocios-                       Página 11 de 20
                                Enfermería Internados



colocación del dispositivo, letra y color, descripción y la indicación del profesional que realizó
la colocación. Es posible eliminar los registros y realizar la exportación a excel o impresión
en PDF.




En caso que el paciente sea neonato o pediátrico, el diagrama varía de la siguiente manera:




           Monitoreos
Cada formulario de monitoreo registra por evento cada medición. Estas quedan ordenadas
cronológicamente dentro de las 24hs.

Se pueden configurar distintos formualarios de monitoreo como por ejemplo Signos vitales,
Glucemia, Monitoreo respiratorio, entre otros.

           Scores
Una vez asociados los scores a realizarse en cada servicio centro, se provee del cálculo
automático derivado de la selección de cada apartado del score, que definirá según el score
utilizado. El registro de scores no puede ser eliminada una vez realizadas pero si se podrá
modificar alguna selección.

El sistema cuenta con scores precargados, que pueden ser agregados según servicio centro.
A continuación, se indican dichos scores:

                             BP-Modelo de Negocios-                     Página 12 de 20
                              Enfermería Internados



Listado de Scores:

                                          GLASGOW Modificado para lactantes y
ABCD²
                                          niños > 1 año
ALVARADO                                  Glasgow-Blatchford Bleeding Score (GBS)
APACHE II                                 GRACE
Apgar                                     HAS-BLED
BISAP para pancreatitis                   HUNT & HESS
Bishop                                    ICH
BMI                                       Índice de Barthel
BODE para COPD                            MARSHALL MODIFICADO
CAM-ICU: Confusion Assessment
                                          MASCC
Method for the ICU
CANADIAN CT HEAD RULE                     MELD Na
Categorizacion pacientes internados       NEWS
CHA2DS2-VASC                              NEXUS
Cl. Creatinina (Cockroft-Gault)           NIHSS
Comorbilidad de CHARLSON                  NRS 2002
Criterios de SGARBOSSA                    PARKLAND para quemaduras
CRUSADE                                   PESI Simplificado
CURB-65                                   PUNTUACION NOVA
Escala de BRADEN                          RANSON en la admisión
Escala de Fuerza Muscular Medical
                                          RANSON 48hs después de admisión
Research Council
Escala de MORSE                           ROCKALL
ESCALA DE NORTON UPP                      RTS
Escala de RAIMONDI                        SAPS II
Escala de TAL (p/ >= de 6 meses)          SOFA
Escala de TAL (p/ menores de 6 meses)     TIMI para NSTEMI
Escala RASS                               TINETTI
                                          TISS-28 (Therapeutic Intervention Scoring
Escala visual analógica (EVA)
                                          System)
FISHER (para HSA)                         WELLS EP
GLASGOW                                   WELLS TVP
GLASGOW Modificado para lactantes y
niños < 1 año


           Evolución y cierre de turnos
El registro de evoluciones son agrupadas en cada uno de los turnos de enfermería
correspondientes. Estos están definidos con una duración de 8 horas, en las cuales se
permite el cierre de cada turno hasta 30 minutos antes de iniciar el turno siguiente. Es
posible configurar el horario de inicio de los turnos de enfermería, los cuales se dividen en
Mañana, Tarde y Noche.

                                     BP-Modelo de Negocios-                            Página 13 de 20
                                      Enfermería Internados



Al cerrar el turno, se podrá ingresar una observación como ultima evolución resumiendo la
ultima evaluación actual del paciente. No se permite cargar una evolución en un turno que
fue cerrado. El cierre de turno puede realizarse individual por paciente o bien genérico. En
este último caso se mostrarán aquellos pacientes que aún no estén cerrados
individualmente y tengan al menos una evolución hecha por el/la enfermero/a en su turno1.

Las evoluciones son de texto libre y pueden ser editadas solo por el usuario que realiza la
carga de la misma. Estas quedarán ordenadas cronológicamente y diferenciadas entre todas
las evoluciones realizadas al paciente. Cabe destacar que estas evoluciones son
visualizadas también desde la internación del paciente y registradas en su historia clínica.

               Documentación
Durante el proceso de evolución del paciente, es posible cargar y/o escanear
documentación adicional respaldatoria, como por ejemplo Autorizaciones, órdenes,
protocolos, etc. Las extensiones admitas son .png,.jpg o .pdf. Una vez cargadas pueden ser
visualizadas y/o descargadas en el ordenador.

      Registro de administración y controles

               Generalidades
En la planilla de registro se verán reflejadas las indicaciones médicas vigentes (con sus
correspondientes modificaciones dentro de las 24hs en tiempo real) junto con los controles
de enfermería. La planilla de enfermería inicia con el horario del primer turno y finaliza con el
horario de fin del último turno de enfermería, se divide por horas visualizando siempre el
rango de hora actual, enmarcado en color verde.

Durante la indicación médica es posible asignar un subtipo de indicación(ej: Antibióticos,
Corticoides, Inmunosupresores, entre otros) mediante las cuales se agruparán en la planilla.




En la planilla se describe como cabecera datos del paciente tal como nombre, apellido, dni,
sexo, fecha de nacimiento, edad, peso, altura, score de enfermería (si aplica configuración).

Se permite navegar entre planillas de enfermería anteriores y posteriores, siempre
mostrando por defecto la planilla actual, que inicia en el horario del primer turno de
enfermería y termina en la última hora del último turno de enfermería. Luego un filtro que
permite mostrar indicaciones vigentes, no vigentes o todas.

Por otro lado, pueden observarse un Histórico de ítems administrados el cual trae todas las
administraciones de insumos registradas al paciente (medicamentos y descartables).

1
    Funcionalidad prevista, aún no implementada a la fecha de presentación del documento.

                              BP-Modelo de Negocios-                      Página 14 de 20
                               Enfermería Internados



Por cada indicación es posible visualizar la fecha de indicación, el profesional que indicó, el
estado de la indicación, el estado del pedido, la traducción y realizar un re pedido a farmacia
central en caso que por algún motivo no haya stock disponible del ítem a administrar.




           Administración con indicación
Al registrar una administración en un horario determinado, tanto para medicación como
controles, se debe seleccionar un bloque horario. Cabe destacar que no se pueden realizar
administraciones posteriores a la hora actual (futuras). Esta puede ser respetada o corrida
según la disposición al momento de registrar las administraciones.

Al administrar se debe completar la dosis administrada e ingresar observaciones si
corresponde. Además es posible cancelar la administración.




                  Administración de multidosis
Se permite la administración de ítems multidosis en caso que el genérico equivalente esté
configurado como tal. Existen distintos tipos de multidosis, a saber:

                                      BP-Modelo de Negocios-                             Página 15 de 20
                                       Enfermería Internados



       Aplicaciones: Aquellos cuya administración es en aplicaciones, tal como cremas y
        ungüentos2.

       Gotas: Aquellos cuya administración es por cantidad de gotas3. Ej: gotas oftálmicas

       Dosis: Aquellos cuya administración es en cantidad de dosis, como los puff e
        inhaladores.

       Volumen: Aquellos cuya administración depende                         de    la    concentración    tal
        como %p/v, %v/v. Ejemplo: jarabes, ampollas, etc

       Unidades: Aquellos que se miden en Unidades internacionales, tal como Insulina.

Para la administración de items multidosis en primer lugar se debe verificar que se tengan
items abiertos, ya sea reservados a paciente como de otro origen. Luego, dependiendo el
tipo de multidosis, se deberán ingresar datos de la administración teniendo como referencia
la cantidad disponible del ítem. Se puede contar con varios items abiertos, y cerrarlos de
manera manual. En caso que se agote las dosis por consumo, se cerrará el item
automaticamente.4

Por otro lado, pueden existir casos que se desee cerrar ítems manualmente (por ejemplo si
desde el sistema se cuenta con cantidad disponible pero en realidad no lo hay). En este
caso debe indicarse el motivo por el cual se cierra el item y confirmar la operación5. Cuando
se cierra un item, se da por consumido y se descuenta del stock.




2y3
       Funcionalidad prevista, aún no implementada a la fecha de presentación del documento.



4 Y5
        Funcionalidad prevista, aún no implementada a la fecha de presentación del documento.

                              BP-Modelo de Negocios-                       Página 16 de 20
                               Enfermería Internados




           Administración directa
Registrar en la planilla de enfermería ítems (descartables) que se encuentren en el depósito
local, que no hayan sido indicados.

Estos pueden agregarse desde un formulario de pedido o de forma individual ítem por ítem.

           Control de enfermería
Registrar acciones de enfermería que se realicen sobre el paciente, independientemente de
las ya indicadas.

           Administración continua
Al registrar una administración continua, la planilla muestra el recorrido completo del tiempo
transcurrido entre el inicio y el fin de la misma.

Al momento de administrar se indica el inicio de la infusión y fin. Al clickear sobre el horario
de inicio de la infusión se abrirá pop-up para indicar detalles de inicio de la perfusión.

Se deberá seleccionar para el consumo si la medicación administrada proviene del depósito
del servicio, reservada a paciente o de algún deposito periférico del sector de internación.

Una vez finalizada, se seleccionará el bloque horario para ingresar el fin de la administración
continua y el correspondiente registro en la planilla.

                             BP-Modelo de Negocios-                      Página 17 de 20
                              Enfermería Internados



   Gestión de depósito

           Pedidos manual
Para la creación de pedidos manuales se debe seleccionar el depósito al cual se realizará el
pedido.

La carga de ítems, puede realizarse de forma individual, seleccionando ítem por ítem, o
masivamente, importando un archivo con el código de ítem y la cantidad necesaria.

La selección de ítems puede realizarse por varias clasificaciones, por ejemplo: genérico,
producto comercial, acción farmacológica, entre otros.

Es posible definir una prioridad por ítem en cada pedido.

Una vez generado el pedido, se debe confirmar para enviar el pedido manual al depósito.
Posteriormente, desde el depósito destino por ejemplo Farmacia central debe prepararse y
entregarse según lo solicitado.




                                                                                        En
                                                                                        caso
                                                                                        que
se desee realizar un pedido manual reservado a un paciente en particular se debe realizar
desde el censo gráfico. Allí deberá buscarse el ítem individualmente o a través de un
formulario pre armado, colocar la cantidad pedida, prioridad y observaciones en caso que
corresponda. Posteriormente, el depósito destino (Ej: farmacia central) deberá preparar y
entregar el pedido al office.

Devolución de medicación reservada a paciente
Se define por Sector Servicio Internación, en qué momento/forma se registra el consumo de
lo administrado por enfermería:

                             BP-Modelo de Negocios-                     Página 18 de 20
                              Enfermería Internados



   Administración Enfermería: al momento de confirmar la administración de
    medicamentos a pacientes internados, el consumo se registra y genera la orden de
    servicio correspondiente.

   En devolución: cuando se genera la devolución de los insumos reservados a pacientes
    internados, se registra el consumo, devolución y su correspondiente orden de servicio.

Para realizar la devolución de insumos reservados a pacientes internados se debe ingresar
al depósito que corresponda con el sector de internación.

Allí se listan todos los pacientes que están o estuvieron internados en el sector que
corresponde a ese depósito que tienen insumos pendientes de devolver.

Si el registro de consumo se realiza en “Administración Enfermería”, se listarán solo los
insumos aún no administrados a paciente desde la planilla de enfermería.

Al momento de realizar la devolución, se completa la cantidad a devolver y se genera el
movimiento de stock correspondiente.

En caso de que la cantidad a devolver sea menor a la reservada, la cantidad restante
quedará pendiente de devolución, al igual que si no se completa dicha cantidad.

Si el registro se realiza “En Devolución”, de acuerdo lo que se complete en “Ctd a devolver”
es el consumo de cada paciente. En cada caso se registra el movimiento de stock que
corresponda.

Ejemplos:

     Si la cantidad reservada es 3 y se devuelve 2, se considera que el paciente consumió
    una unidad del insumo. En ese momento se genera la Orden de Servicio por dicho
    insumo y se devuelven dos unidades.

     Si Ctd. Reservada es 4 y Ctd a Devolver 0 (cero), es interpretado que el paciente
    consumió el total de los insumos, generando una orden de servicio por 4 unidades.

     Si no se completa la cantidad a devolver, no se genera movimientos ni órdenes de
    servicio, ya que se considera que la misma será devuelta o consumida en otro momento
    (Devolución Parcial)

     Es posible registrar el consumo de todos los ítems (completar “Ctd. A Devolver” con
    cero), utilizando el botón “Marcar para consumo”.

A continuación, se muestra diagrama BPMN de dicho proceso:

                              BP-Modelo de Negocios-                       Página 19 de 20
                               Enfermería Internados




           Ajustes


El sistema ofrece la posibilidad de realizar ajustes por ítems individuales. Allí se establece si
el movimiento es un ingreso o egreso y se debe indicar el sub-tipo de movimiento, el cual
puede ser, por ejemplo: control de inventario, ingreso por proveedores, etc. Los sub-tipo de
movimientos son configurables por el usuario.



   Consultas
Se pueden realizar consultas del Depósito principal con relación al stock, farmacia, compras,
necesidades de compras, entre otras. Serán descritas a continuación.

           Consulta de movimientos de stock
Este tipo de consulta permite al usuario visualizar los movimientos de stock realizados
desde el depósito donde el usuario se encuentra logueado. Se puede filtrar por:

a)Tipo de movimiento

b)Subtipo de movimiento

c)Periodo de fecha

d)Producto comercial

                             BP-Modelo de Negocios-                     Página 20 de 20
                              Enfermería Internados



e)Tipo de ítem o sub tipo de ítem

f)Si el ítem es de alto costo o de consumo variable

Se contemplan además, las cantidades de ingreso y egreso por producto, el origen y destino
del movimiento de stock, la descripción del ítem y genérico.

Consulta Mov. Stock por tipo de Mov.

Permite observar los movimientos de los ítems que se realizaron, agrupados por tipo de
movimientos. A su vez este tipo de consulta requiere que se ingresen los siguientes filtros:

a)Tipo de movimiento y subtipo de movimiento

b)Egreso/ingreso

c)Periodo de fecha

           Stock depósito
Esta consulta permite al usuario conocer la cantidad en stock en el depósito en el que se
encuentra logueado. Se podrá filtrar la búsqueda para realizar la consulta:

a)Por tipo de ítem o sub tipo de ítem

b)Si el ítem es de alto costo o consumo variable

Al realizar la consulta podrá obtener datos de relevancia relacionados al ítem como:
cantidad disponible del ítem, reservada en internado y cirugía, en consignación, de provisión
externa, en préstamo y tránsito.

           Stock Item
La consulta stock item permite al igual que la anterior revisar los stocks de medicamentos
pero en todos los depósitos. Podrá filtrar por:

c)Tipo de ítem o sub tipo de ítem

d)Alto costo o consumo variable

Al realizar la consulta obtendrá los datos de relevancia que se mencionaron anteriormente
pero además cuenta con información sobre el Depósito.

           Consulta ítem por vencer
Este tipo de consulta devuelve la lista de productos con fecha próxima a vencer de acuerdo
a la ingresada como filtro.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
