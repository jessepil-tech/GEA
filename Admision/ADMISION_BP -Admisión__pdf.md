Fuente original: BP -Admisión.pdf
Ruta original: BP -Admisión.pdf
Formato original: PDF

BP – Modelo de Negocios


              Admisión
                  Versión 1.0

 INFORMACIÓN DEL DOCUMENTO

                Título del Documento         BP – Admisión

Información     Localización           del
General         documento




Preparado por   Nombre (Empresa)                 Gerencia         Rol                  Fecha




                Luciana Estevarena               Soporte          Análisis Funcional   18/01/22
                                                 Funcional

                Catalina Claucich                Implementación   Análisis Funcional   24/01/23




Revisado por    Nombre (Empresa)                 Gerencia         Rol                  Fecha




                Catalina Claucich                Implementación   Analista funcional   24/01/23

                Silvana Elizondo                 Implementación   Analista funcional   27/01/23




Aprobado por    Nombre (Empresa)                     Proceso              Rol          Fecha

Documento        Versión     Motivo del Cambio                                     Fecha Efectiva


Historia




 Objetivos


     El proyecto de implementación al que este documento pertenece tiene como principales
 objetivos:


       integrar, optimizar y automatizar el proceso de admisión de pacientes.
       Implementar el módulo de Admisión en el entorno del cliente de una manera
           eficiente.
       Garantizar el uso correcto por parte del cliente, en relación al módulo de Admisión.


 Objetivo del documento “BP – Procesos de negocio”:

     El presente documento tiene como objetivo principal informar cada uno de los procesos
 que se llevarán a cabo –haciendo uso del sistema al implementar– en las áreas incluidas en
 el alcance de este documento, así como las interfaces necesarias con otros sistemas
 informáticos implementados en la institución.

     La correcta documentación de estos procesos está sujeta a las reuniones continuas con
 los referentes de cada área –cada una mapeada en uno o más módulos del sistema– para
 relevar los distintos procesos a fin de identificar las posibles brechas funcionales a cubrir,
 así como las oportunidades de cambio y mejora. Cada proceso plasmado en este
 documento debe ser aprobado por el referente designado, y todo aquello que no se registre
 adecuadamente en esta instancia puede suponer una demora en los tiempos de
 implementación a futuro.

Alcance


  En el siguiente documento se describirán los procesos que se llevan a cabo dentro del
módulo Admisión contemplados en el sistema Thinksoft HIS:

   1. admisión de pacientes;


   2. reserva de internación;

   3. derivación de internación externa;


   4. altas;

   5. gestión de interconsultas;


   6. facturación de extras;

   7. gestión de camas;


   8. consultas.

   Admisión Internados


Conceptos iniciales


Antes de comenzar a revisar los procesos contemplados por el módulo, se presentan
conceptos fundamentales relacionados con la admisión. Los mismos se retomarán y
revisarán en profundidad en la configuración del módulo.


   ━   Función Internación
La función de internación relaciona las prestaciones de pensión o de terapia/rehabilitación
que se cargan en cada cama e impactarán en facturación.
Estas prestaciones hacen referencia a:
- Prestación Pensión: prestación que es generada cada día de internación en los intervalos
estipulados como Check In y Check Out durante el período de vigencia de la misma.
- ARM – Incubadora – Luminoterapia – Óxido nítrico: prestaciones que luego de ser
habilitadas, se gestionan para su facturación a través de la registración de una fecha de
inicio y fin en el censo gráfico. En general el inicio y fin de estas prestaciones está
controlado por el área de enfermería.


Existe también un parámetro de “Nursery” con el fin de que las camas vinculadas a esta
función no sean contabilizadas en el libro de internación.
Asimismo, la función puede tener asociados prestaciones o ítems con el fin de que los
mismos sean generados para su facturación cada día con el mismo mecanismo de la
prestación pensión.


      Tipo Internación
En Admisión internados » Configuración » Tipo Internación puede configurarse cada tipo de
internación. Cada tipo de internación está relacionado con un tipo de admisión; el sistema
de hospital contempla 3 tipos de admisiones posibles:
- HOSPITALARIA
- TRANSITORIA
- AMBULATORIA


Crear un tipo de internación permite diferenciar la información estadística en, por ejemplo,
internaciones clínicas o quirúrgicas. El tipo de internación también será utilizado en
asociaciones posteriores. Los campos que pueden configurarse por tipo de internación se
listan a continuación.

       ━Requiere alta médica: alta médica obligatoria para el egreso del paciente.
       ━Requiere cama: asignación obligatoria de una ocupación al realizar la admisión.
       ━Requiere domicilio: campo obligatorio al realizar la admisión.
       ━Requiere institución derivante: campo obligatorio al registrar una derivación
       externa.
       ━Requiere motivo internación: campo obligatorio al realizar la admisión.
       ━Requiere responsable administración: campo obligatorio al realizar la admisión.
       ━Requiere libro internación: este tipo internación genera registro en libro de
       internación.
       ━Requiere diagnóstico al alta: campo obligatorio para otorgar el alta.
       ━Requiere convenio en reserva de internación: campo obligatorio ingresar
       reserva.
       ━Requiere epicrisis: generación del alta mediante epicrisis obligatoria.
       ━Requiere médico derivante: campo obligatorio al realizar la admisión.
       ━Requiere responsable internación: campo obligatorio al realizar la admisión.
       ━Requiere teléfono: campo obligatorio al realizar la admisión.


      Tipo Internación por Servicio Centro
En Admisión internados » Configuración » Tipo de Internación por Servicio Centro se
genera la asociación del Tipo de internación con la función de internación para cada servicio
centro de internación.
Todas las prestaciones definidas en la función junto con los parámetros del tipo de
internación serán gestionados para cada paciente admisionado en ese servicio de
internados.
A su vez, en esta pantalla también se puede definir:
   ━   control de edad obligatorio y rango específico: se restringe la admisión a dicho
       servicio si el paciente no se encuentra en el rango del control.
   ━   Control de sexo: se restringe la admisión a dicho servicio si el paciente no
       corresponde al sexo especificado.
   ━   Porcentaje de reserva: parámetro que permite llevar una alerta visual en las
       pantallas de “Disponibilidad Actual” y “Reservas Internación” cuando se supera el
       porcentaje de reservas definido.
   ━   Permite internar bebé: check que permite habilitar la internación de recién nacidos
       en el servicio (Pacientes con tipo documento nomenclado como “BEBE…”.
   ━   Permite binomio: check que permite la internación conjunta en el servicio.

   1. Admisión del paciente


  La admisión comienza con el apersonamiento del paciente, con una solicitud que puede
ser o no programada (ya sea de la propia institución o de derivaciones externas), lo cual
implica que puede haber una solicitud de internación previa a la llegada del paciente.




  El admisionista posee una pantalla de trabajo segmentada en navegadores que le
permiten no solo gestionar un ingreso y reservas existentes, sino también visualizar la
disponibilidad actual de todas las ocupaciones, egresos esperados, ingresar nuevas
reservas, gestionar asignación de interconsultas o estudios externos.

  En esta instancia, la identificación del paciente es de suma importancia, debido a que
una identificación incorrecta produce la segmentación de la HC (evento que estará aislado

de los anteriores), independientemente de que luego pueda unificarse a futuro si es
detectada esta situación. Por ese motivo se dispone de la lectura del DNI como medio
seguro para la lectura y confirmación automática de los datos del paciente. Se sugiere
utilizar esta herramienta para la búsqueda de paciente.

   Si el paciente posee una solicitud de internación existente, se gestionará su ingreso a
través de la misma, verificando y agregando la información requerida. Si no posee solicitud
de internación previa, es posible realizar un ingreso sin reserva.


   Dependiendo del financiador del paciente, el sistema indicará qué información o
documentación debe cargarse de manera obligatoria. Asimismo, se realizarán las
validaciones correspondientes, como la de elegibilidad.

       1.1. Carga de datos en la admisión


   A la hora de admisionar un paciente es necesario cargar datos, algunos de ellos son
obligatorios, otros opcionales.


   Estos datos se dividen en secciones específicas, de la siguiente manera:

       Datos del paciente:
       - datos personales (tipo y número de documento, nombre y apellido, sexo, fecha de
       nacimiento, estado de confirmación de los datos),
       - filiatorios y de facturación (convenio, plan, número de afiliado, tipo de afiliado,
       condición de IVA, CUIL),
       - correo electrónico y domicilio.


       Teléfonos: datos de contacto telefónico del paciente.


       Datos internación: se deben ingresar los datos administrativos más relevantes para
       generar la admisión, como tipo de admisión, tipo de internación, función de
       internación, origen de la internación, el médico que solicita y el que recibe al
       paciente, motivo y la declaración de alergia. Este último parámetro permite, en caso
       de ser declarada, imprimir una pulsera de color característico para que se defina el
       tipo de alergia durante la internación). Opcionalmente puede agregarse una persona
       de contacto.

Datos internación conjunta: permite visualizar un vínculo de internación conjunta o
generar uno nuevo (controlando que las internaciones sean independientes una de
la otra).


Responsables: asignación del responsable de la internación y de los cargos
percibidos a facturarse (que no se encuentren cubiertos por el financiador). Puede
ser el propio paciente u otra persona.


Acompañante: asignación y definición de quienes serán los acompañantes del
paciente.


Documentación: importación de toda la documentación relevante del paciente y de la
internación. Puede escanearse o ser cargada de una carpeta raíz.


Documentación Requerida por Plan Convenio: visualización de la documentación
obligatoria a presentarse, definida en la parametrización de cada financiador. Aquí
puede especificarse si se recibió la documentación requerida.


Lugar de internación: opcionalmente, se puede asignar la ocupación al paciente al
momento de realizar la internación. Para ello se solicitará fecha y hora, junto con la
selección de cama del listado de camas disponibles. Si no hay camas disponibles
del servicio de internación solicitado para el paciente, se podrá habilitar selección
dentro de la disponibilidad de todos los demás servicios.


Orden de internación: si el paciente dispone de una orden de internación, podrá
cargarse. La carga de la orden debe realizarse luego del registro de la admisión y
puede gestionarse a través de modelos ya predeterminados. Cargar una orden de
internación implica que lo que esta especifique reemplazará la cobertura del
paciente.


Extras Previstos: carga de los extras que se estipula que serán consumidos por el
paciente en su internación. Ej.: cama o comida de acompañantes, comidas
especiales para el paciente.


Datos Policiales: datos específicos a ingresar si la admisión es derivada de algún
siniestro o evento con intervención policial.

Al finalizar el ingreso de todos los datos relevantes se solicitará, si así estuviera
determinado, que el paciente realice un pago a cuenta (configurable a nivel de tipo de
internación por servicio centro). Si el paciente rechaza abonar el monto, se cancelará el
proceso de admisión. En caso de que el paciente acceda a abonar lo informado por la
admisión se realizará la impresión de las pulseras de internación y toda documentación
previamente definida.


       1.2. Visualización y modificación de información de pacientes internados


Existe una sección específica (“Pacientes internados”), en la cual se pueden modificar los
datos que fueron ingresados en el proceso de admisión del paciente.
Se busca en el listado de internados el paciente cuya información se desea editar, se
modifican los datos y luego se actualiza la admisión. También se pueden volver a imprimir
los formularios de admisión que se necesiten o la pulsera del paciente.


       1.3. Estados del paciente internado

Aquellos pacientes que no cuenten con ocupación actual, tendrán alguno de los siguientes
estados:

      EN TRÁNSITO: paciente que luego de ser admisionado, tuvo movimiento en el
       histórico de ocupaciones y actualmente se encuentra sin cama, a la espera de una
       nueva asignación.
      HOSPITALARIA: paciente de ese tipo internación que nunca tuvo asignación de
       ocupación.
      AMBULATORIA: paciente de ese tipo internación que nunca tuvo asignación de
       ocupación.
      TRANSITORIA: paciente de ese tipo internación que nunca tuvo asignación de
       ocupación.
      BEBE EN TRÁNSITO: paciente recién nacido que proviene de una reciente
       desvinculación de la internación conjunta y se encuentra a la espera de una nueva
       asignación o el alta definitiva

   2. Reserva de internación


Se puede realizar una reserva para la internación de un paciente, por ejemplo, en aquellos
casos en que se trate de una internación programada.


Para ello se solicitan los siguientes datos obligatorios:


          fecha de ingreso y cantidad de días;
          tipo de admisión y tipo de internación;
          servicio y función de internación;
          convenio y plan;
          motivo de internación.


Y de manera opcional:
          número de afiliado;
          observaciones;
          operador externo solicitante de la internación;
          referencia persona externa que solicita la internación;
          paciente. Si no existe en la base de datos, puede ingresarse como nuevo con:
           tipo y número de documento, apellido y nombre, fecha de nacimiento y sexo.


Se podrá observar la disponibilidad actual antes de ingresar la reserva, visualizando allí
también, con una referencia de color, los servicios que tienen definido un porcentaje de
reserva como alarma entorno a la ocupación de camas. Con esto quedará registrada la
reserva, que luego será visualizada según parámetros en las áreas de reserva de la pantalla
principal del admisionista, para gestionar su ingreso el día indicado.

   3. Solicitud de internación externa (derivación)


El sector a cargo de registrar las derivaciones puede solicitar una derivación externa. En
ese caso, se solicita ingresar los siguientes datos:
        paciente. Si no existe en la base de datos, puede ingresarse como nuevo con:
           tipo y número de documento, apellido y nombre, fecha de nacimiento y sexo.
        Convenio, plan, tipo y número de afiliado.
        Tipo de admisión y tipo de internación.
        Servicio y función de internación.
        Fecha de solicitud.
        Institución que deriva.
        Profesional y matrícula.
        Motivo de internación y diagnóstico.
        Cantidad de días.
        Contacto del derivante.

Una vez registrada la solicitud, puede ser autorizada, derivada anulada o rechazada, según
corresponda. Si la misma es autorizada a ingresarse, luego podrá gestionarse su circuito
administrativo desde la pantalla principal del admisionista.

   4. Alta del paciente


En el sistema se admiten tres tipos de alta y existe una pantalla específica en la admisión
para gestionar estas altas. Esta pantalla se encuentra al visualizar todos los datos de
internación del paciente.

      Alta médica. Consiste en una aprobación por parte del equipo médico, que indica
       que el paciente ya se encuentra en condiciones de egresar de la institución. Es
       posible configurar la obligatoriedad de este alta por tipo de internación. Este alta
       puede otorgarse desde el episodio de internación del paciente o desde la admisión.
      Alta administrativa. Indica que el paciente se encuentra administrativo listo para
       egresar de la institución, es decir, que no cuenta con deudas de dinero o de
       documentación. Si el paciente debe abonar adicionales o conceptos no cubiertos por
       el financiador, el responsable de internación deberá dirigirse a la caja
       correspondiente a saldar la diferencia de su cuenta corriente. Este alta se da desde
       la admisión.
      Alta física. Implica la liberación de la cama luego de que el paciente cuenta con el
       alta médica. Este alta puede darse desde el censo gráfico o desde la admisión.

El proceso de egreso comienza con el alta médica, la cual es realizada por un médico
responsable. El alta médica inhibe la realización de indicaciones ya sea de medicamentos o
de estudios.

Una vez constatada la desocupación real del ambiente, el personal a cargo del paciente
procede a realizar el alta física, liberando la cama en el sistema e iniciando el proceso de
preparación de la ocupación para ser asignada nuevamente.

   5. Gestión de interconsultas
La gestión de las interconsultas se realiza desde el módulo de admisión. Es posible
visualizar todas las solicitudes de interconsulta pendientes y, a partir de estas, realizar la
asignación (por profesional o especialidad) o la anulación de la solicitud.

Las interconsultas pueden tener distintos estados:
         solicitada: se encuentra pendiente de asignación.
         Asignada: con profesional/especialidad) asignado, pero pendiente de realizar.
         Realizada: profesional/especialidad se ha asignado y la interconsulta está
           realizada.
         Anulada: solicitada y luego anulada.

Dentro de la información que puede visualizarse en relación a las interconsultas, existe un
historial de estado, en el cual se adjuntan observaciones, reasignaciones o cambios de la
solicitud si los hubiera.

   6. Facturación de extras


En el módulo de admisión se permite la carga de extras para una internación. Esta carga
puede realizarse al momento de admisionar al paciente o durante el transcurso de su
internación.
La carga de extras consiste en determinar cuál es el extra que se añadirá, la fecha en la
cual inicia la vigencia y la cantidad de días previstos.
Posterior a la carga de los extras, es necesaria la confirmación de los mismos para su
correspondiente facturación en la caja correspondiente.

   7. Gestión de camas


El sistema cuenta con una pantalla de visualización y gestión de las camas: el censo
gráfico. El mismo permite visualizar en tiempo real todos los ambientes y camas por sector
de internación y servicio. Asimismo, otorga una interfaz gráfica de indicadores/alarmas que
se utilizan en la gestión médica/administrativa (alergias, solicitudes de estudios y
medicación con sus vigencias, aislamientos, altas, etc).

Dentro de la gestión administrativa, permite agilizar el proceso de gestión de camas
mediante la interacción directa con las distintas ocupaciones. En cada cama se visualizan
distintas secciones con información y tareas a ejecutar. Algunas de estas son:

      datos del paciente: información relevante del paciente (nombre, apellido, tipo y
       número de documento, convenio, diagnóstico, etc.).
      Liberar Cama: desocupación de la cama para dar lugar a su preparación y nueva
       ocupación. Si ya se ha dado alta médica (según la configuración), la liberación de la
       cama otorga el alta física.
      Cambiar Cama: asignación de una nueva cama disponible. El paciente quedará
       asignado, según el caso, al servicio vinculado de la nueva ocupación.
      Cambiar función Cama: asignación de una nueva función a la ocupación, la cual
       comenzará a regirse por las prestaciones asociadas de la nueva función.
      Solicitar housekeeping: solicitud de limpieza de la cama.

   8. Consultas


Existen diversas consultas que brindan información relacionada con la admisión y la gestión
administrativa de las internaciones. Se describen a continuación las más importantes.

Consulta Censo General
La consulta devuelve información administrativa de todas las internaciones en una fecha y
hora específicas. En esta pantalla se puede observar el total de internados por sector de
internación, las altas previstas para el día de la búsqueda y el día anterior, así como los
óbitos de ambos días. Se puede exportar la información en excel o emitir un reporte
impreso.

Consulta Generador Censo
La consulta devuelve información administrativa de todas las internaciones en una fecha y
hora específicas. Esta pantalla suele utilizarse para conocer cuál es la disponibilidad y la
ocupación de camas ya que permite obtener una foto de la ocupación al momento de
búsqueda.

Altas entre fechas
Se visualizan las altas realizadas entre las fechas seleccionadas con la posibilidad de
agregar filtros para realizar la búsqueda.

Ingreso entre fechas
Se visualizan los ingresos realizados entre las fechas seleccionadas con la posibilidad de
agregar filtros para realizar la búsqueda.

Consulta de pedidos de interconsultas
Se visualiza el estado de las interconsultas que fueron solicitadas con los detalles de cada
paciente y de su internación.

Consulta de camas por estado
Permite filtrar por sector, función y tipo de admisión las camas según su estado.

Libro de internación
Se visualizan todos los ingresos mensuales de internaciones, con posibilidad de utilizar los
ordenadores de columnas e imprimir el reporte si fuera necesario

Housekeeping
Esta consulta permite conocer por fecha y sector las solicitudes de housekeeping, el horario
en que se solicitó y la duración de la misma, entre otros datos de la cama y el paciente.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
