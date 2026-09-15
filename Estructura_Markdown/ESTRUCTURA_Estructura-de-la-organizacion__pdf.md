Fuente original: Estructura de la organización.pdf
Ruta original: Estructura/Estructura de la organización.pdf
Formato original: PDF

Configuración inicial
  Estructura de la
  organización



                 Versión 1.0

Información del Documento
                 Título del Documento         Estructura de la organización


 Preparado por           Nombre (Empresa)              Gerencia               Rol              Fecha




                 Bioing. Catalina Claucich         Coordinación de    Analista Funcional   17/11/22
                                                   implementación

                 Ing. Silvana Elizondo             Implementación     Co responsable de    24/11/22
                                                                      proyecto




 Revisado por            Nombre (Empresa)               Gerencia               Rol             Fecha




 Aprobado por

                           Nombre (Empresa)                   Proceso                Rol      Fecha




  Documento         Versión                          Motivo del Cambio                     Fecha Efectiva


   Historia      1.0.0

 1. Generalidades

a. Nomenclador

 El conjunto de todas las practicas que realiza y/o factura la organización constituye el nomenclador
 de la organización.
 Dentro del nomenclador de la organización se encuentran las prestaciones nomencladas (definidas
 en el nomenclador nacional), no nomencladas (agregadas al nomenclador nacional) y módulos
 (conjunto de prestaciones).
 El nomenclador de la organización solo define el código y descripción de las prestaciones que
 realiza la organización y no tiene valores o precios asociados.
 Si un convenio requiere facturar un código diferente al definido en el nomenclador de la
 organización, se debe utilizar la homologación de prestación por convenio que provee el sistema.




b. Empresa

 El sistema permite la operación multiempresa. Entendiendose a cada empresa como una unidad de
 gestión administrativa independiente.
 Cada Empresa tiene una Razón Social y CUIT diferentes
 Posibilita también definir según necesidad, la división en sucursales establecidas físicamente cuyas
 actividades administrativas y financieras tienen un impacto individual con su entorno tanto a niveles
 de facturación como de gestión, pero desempañando la misma función que el establecimiento del
 cual depende (empresa o sucursal principal).
 Para cada empresa se definen los punto de venta declarados en AFIP y los parámetros de
 trazabilidad necesarios para la conexión vía web-service con el ente regulador (ANMAT).


c. Nómina del personal

 Se debe definir la nomina completa del personal que se encontrara vinculado a los procesos
 asistenciales, administrativos y de sistemas en el HIS.
 Es necesario registrar los datos principales de cada usuario:
 Apellido, Nombre, Tipo y Nro de Documento, Fecha de Nacimiento y Sexo.
 Complementario a esto, es necesario conocer el rol de cada uno de ellos en cuanto al uso del
 sistema (Médicos, Recepcionistas, Personal de Enfermeria, Facturistas, etc).
 En el caso del personal asistencial, la matricula sera obligatoria para registrar evaluacion sobre el
 paciente.

d. Sucursal

 Se define la sucursal como un establecimiento que actúa con cierta autonomía aunque de forma
 subordinada a una empresa o casa matriz. Es decir, si bien se rigen por una normativa general, en
 ciertos aspectos poseen organización y/o administración independiente, por ejemplo depósitos
 principales.

e. Centro de atención

Corresponden a los edificios físicos donde se realizan las atenciones. Si un hospital posee
consultorios externos en otro edificio, esto corresponderían a dos centros de atención, el hospital y
los consultorios externos.
Los centros de atención deben tener dirección física, y son utilizados para guiar al paciente que
requiere turnos, donde debe presentarse para la atención.




f. Servicio centro

 En atención ambulatoria/internados, el servicio es la unidad mínima de agrupación de operación y
 facturación.
 Se recomienda la definición de servicios separados con el fin de evitar un servicio generalizado que
 conlleve una configuración de mayor complejidad.
 Existen casos como la guardia que esta conformado por distintas especialidades pero que conlleva
 un solo evento de atención asistencial y de facturación.

g. Prestaciones y personal por servicio

 Prestaciones por servicios centro:
Definir por cada servicio en cada centro solo las prestaciones que realiza o factura.
Ejemplo en guardia, los pedidos de laboratorio se facturan en un único evento como servicio de
guardia, a los valores pactados para ese servicio.

 Personal por servicio centro:
Se debe especificar el personal que realiza alguna intervención con el servicio.
Pueden excluirse los que no realicen prestaciones.

h. Recepciones, cajas, buzones de autogestión
 Recepciones de pacientes ambulatorios:
 Las recepciones deben coincidir con los lugares físicos donde el paciente se presenta para solicitar
 la atención.
 Una recepción puede recepcionar a varios servicios y tener varios puestos de atención.
 Mas de una recepción puede recepcionar al mismo servicio.

 Cajas:
 La cantidad de cajas por recepción se determina por la cantidad de cierres de caja que se realizan.
 Si existen tres recepcionistas pero se realiza un solo cierre de caja, esto es una caja con tres
 cajeros simultáneos.
 Si en cambio, se realiza un cierre de caja para cada recepcionista en forma individual, esto son tres
 cajas.
 Para cada caja se deberá definir el punto de venta asociado para el caso de emisión de
 comprobantes fiscales.
 Pueden definirse también cajas de internación para el cobro de garantías, pagos adelantados, etc.

   Buzones de auto-gestión por recepción y áreas de espera


i. Depósito

Se define cada espacio físico que agrupe algún tipo pasible de ser stockeado y movilizado en su
inventario.
Estos contemplan tanto depósitos principales (Farmacia central o Depósitos en office de enfermería)
como secundarios (carros de paro).
Poseen diferentes habilitaciones de movimientos y de ítems según el tipo de deposito.

j. Vademecum hospitalario

 El vademécum hospitalario define todos los tipo de insumos de la organización.
 Pueden estar o no activos para su uso y discontinuarse con el tiempo.
 El stock a definirse puede tener una combinación entre genéricos y productos comerciales.
 La codificación de cada insumo puede estar o no basada en un manual farmacéutico (Ej: Alfabeta).
 Si requiere, puede ya definirse el sistema de clasificación por código ATC.
 Se contemplan diferentes tipo de trazabilidades tales como medicamento y producto por ANMAT
 hasta vencimientos por fecha, lote, etc.

k. Admisión de pacientes internados

 Las admisiones deben coincidir con los lugares físicos donde el paciente se presenta para
 gestionar una internación.
 Estas gestionan todos los servicios de los distintos tipos de internación (Hospitalaria, Ambulatoria,
 Transitoria, Domiciliaria).
 Por cada centro de atención se estructuran también la cantidad de puestos (con sus cajas si asi
 correspondiera) y el personal que trabaja en los mismos.

2. Estructura de la organización: Internación

Servicio
Se define el nombre del servicio de internación. Esto se determina en Admisión internados »
Configuración »Servicio.




Servicio Centro
Permite asociar un servicio con un centro de atención. Esto se realiza en Admisión internados »
Configuración » Servicio Centro.


Deben indicarse de forma obligatoria los parámetros de:
   •   Centro de atención al que pertenece el servicio a definir.
   •   Empresa y sucursal del servicio-centro.
   •   Si el servicio es únicamente ambulatorio, internado o de ambos.
   •   Tipo de servicio (referente a donde será dirigido el uso del servicio según el modulo a
       utilizarse).




Función Internación
En Admisión internados » Configuración » Función internación puede configurarse cada Función
internación, que luego podrá ser asociada a diferentes tipos de prestaciones fundamentales en los
distintos tipos de internación.
Estas prestaciones hacen referencia a:
      Prestación Pensión: prestación que es generada cada día de internación en los intervalos
       estipulados como Check In y Check Out durante el periodo de vigencia de la misma.

      ARM-INCUBADORA-LMT (Luminoterapia)-OXIDO NITRICO: prestaciones que luego de
       serhabilitadas, se gestionan para su facturación a través de la registración de una fecha de
       inicio y fin enel censo gráfico.


Existe también un parámetro de “Nursery” con el fin de que las camas vinculadas a esta
función no seancontabilizadas en el libro de internación.
Asimismo, la función puede tener asociados prestaciones o ítems con el fin de que los
mismos seangenerados para su facturación cada día con el mismo mecanismo de la prestación
pensión.




Tipo Internación
En Admisión internados » Configuración » Tipo Internación puede configurarse cada tipo de
internación.El sistema de hospital contempla 3 tipos de admisiones posibles:
      HOSPITALARIA
      TRANSITORIA
      AMBULATORIA


Se crea para cada una de las anteriores un tipo de internación que permita diferenciar,
mediante unaasociación posterior, los siguientes parámetros para cada servicio de internación:




   1- Requiere alta médica: alta médica obligatoria para el egreso del paciente.
   2- Requiere cama: asignación obligatoria de una ocupación al realizar la
   admisión.3- Requiere domicilio: campo obligatorio al realizar la admisión.
   4- Requiere institución derivante: campo obligatorio al registrar una derivación
   externa.5- Requiere motivo internación: campo obligatorio al realizar la
   admisión.
   6- Requiere responsable administración: campo obligatorio al realizar la
   admisión.7- Requiere libro internación: este tipo internación genera libro de

   internación.

   8- Requiere diagnóstico al alta: campo obligatorio para otorgar el alta.
   9- Requiere convenio en reserva de internación: campo obligatorio ingresar
   reserva.10- Requiere epicrisis: generación del alta mediante epicrisis
   obligatoria.
   11- Requiere médico derivante: campo obligatorio al realizar la admisión.
   12- Requiere responsable internación: campo obligatorio al realizar la
   admisión.13- Requiere teléfono: campo obligatorio al realizar la admisión.


Tipo Internación por Servicio Centro
En Admisión internados » Configuración » Tipo de Internación por Servicio Centro se genera la
asociacióndel Tipo de internación con la función en cada servicio centro de internados.
Todas las prestaciones definidas en la función junto con los parámetros del tipo de
internación serángestionados para cada paciente admisionado en ese servicio de internados.
A su vez, en esta pantalla también se puede definirse:
      Control de edad obligatorio y su rango específico: se restringe la admisión a dicho
       servicio si el paciente no se encuentra en el rango del control.
      Control sexo: se restringe la admisión a dicho servicio si el paciente no corresponde al
       sexo especificado.
      Porcentaje de reserva: parámetro que permite llevar una alerta visual en las pantallas de
       “Disponibilidad Actual” y “Reservas Internación” cuando se supera el porcentaje de
       reservas definido.
      Permite internar bebé: check que permite habilitar la internación de recién nacidos en el
       servicio (Pacientes con tipo documento nomenclado como “BEBE…”.
      Permite binomio: check que permite la internación conjunta en el servicio.


Tipo Ambiente
Aquí se definen los distintos tipos de ambientes (ocupaciones) que existen en la estructura
del área deinternación. Esto se realiza en Admisión internados » Configuración » Tipo ambiente.




Sector Servicio Internación
En Admisión internados » Configuración » Sector Servicio Internación puede delimitarse por
sector laestructura del área de internación según los servicios y office-enfermería para un Centro
de Atención.

Ambiente Internación
En Admisión internados » Configuración » Ambiente Internación se realiza la definición de las
ocupacionesespecificas de cada servicio de internación (según función y tipo de internación).
Se le asigna: una descripción a modo de nombre, el tipo de ambiente de la ocupación, número
de orden entorno a la visualización en el censo gráfico y la cantidad de camas que contendrá el
mismo.




En esta instancia solo se determina la cantidad de camas que conformarán cada ocupación.

3. CHECKLIST

  Generales

  1. Nomenclador de prestaciones de la organización
  2. Empresas
  3. Centros de Atención
  4. Servicios por Centro de Atención
  5. Sectores y ambientes de atención.
  6. Prestaciones por servicios por Centro de Atención
  7. Personal por servicio por Centro de Atención
  8. Recepciones y puestos de atención de pacientes ambulatorios
  9. Buzones de auto-gestión por recepción y áreas de espera
  10. Cajas por centro de Atención asociadas a cada recepción

  Internación

  1. Servicios
  2. Servicio centro
  3. Función de internación
  4. Tipo de internación
  5. Tipo de internación por servicio centro
  6. Tipo ambiente
  7. Sector servicio internación
  8. Tipos de Ambientes de Internación
  9. Ambientes de Internación
  10. Sectores admisión: puestos, personal y cajas
  11. Sectores atención ambulatoria: áreas, consultorios, días y horarios de atención, cajas.
  12. Logo del sanatorio

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
