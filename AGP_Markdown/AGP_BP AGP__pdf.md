Fuente original: BP AGP.pdf
Ruta original: AGP/BP AGP.pdf
Formato original: PDF

          Portal de autogestión del
                          paciente
Versión 1.0




                                  1

 INFORMACIÓN DEL DOCUMENTO

                Título del Documento      Portal de autogestión del paciente




Preparado por   Nombre (Empresa)              Gerencia           Rol                  Fecha




                Ing. Catalina Claucich        Implementación     Análisis Funcional   21/12/2022




Revisado por    Nombre (Empresa)              Gerencia           Rol                  Fecha




Aprobado por    Nombre (Empresa)                   Proceso               Rol          Fecha




Documento       Versión       Motivo del Cambio                                       Fecha Efectiva


Historia




                                                                                                   2

Información del Documento   2

Objetivos                   4

1. Registro                 5

2. Log in                   9

3. Configuración            12

4. Turnos                   16

5. Estudios                 23

6. Atenciones               25

7. E-consulta               26

8. Medicamentos             28

9. Notificaciones           31




                            3

Objetivos
Este documento tiene como objetivo presentar las funcionalidades del portal de autogestión
del paciente (AGP).




                                                                                        4

   1.Registro
Para registrarse en el AGP, un paciente debe:

   1) Ingresar a la aplicación (Fig. 1)




               Figura 1. Pantalla de registro e ingreso al portal de pacientes.




   2) Iniciar la carga de datos para realizar el registro.
          A. Ingresar una dirección de correo electrónico y contraseña (Fig. 2).




                                                                                   5

                     Figura 2. Formulario de registro: Datos de la cuenta

En esta instancia existe una verificación, la cual informa al paciente si el mail ingresado ya
se encuentra asociado a una cuenta del AGP.

           B. Ingresar datos identificatorios y de contacto (Fig. 3).




                                                                                                 6

         Figura 3. Formulario de registro: Datos del paciente

C. Configurar qué medios de comunicación desea activar para recibir
   notificaciones (Fig. 4).




           Figura 4. Formulario de registro: Comunicación


                                                                  7

Aclaración: la configuración de “Mensajes de texto” impacta también en las notificaciones de
Whatsapp.

            D. Confirmación de datos ingresados.

Una vez realizados todos los pasos, el paciente recibirá un correo electrónico a la dirección
ingresada en el paso A, con el fin de validar su cuenta. Una vez realizado,, podrá ingresar al
portal para comenzar a operar.




                                                                                                8

   2.Log in
Una vez que el paciente se haya registrado en el portal, podrá acceder a su cuenta
ingresando correo electrónico y contraseña.




En caso de que haya olvidado su contraseña, debe seleccionar la opción “¿Olvidó su
contraseña?”.




                                                                                9

Acto seguido deberá ingresar el mail con el cual está registrado al portal. Recibirá allí un
correo electrónico con las instrucciones a seguir para reestablecer su contraseña.




                                                                                         10

Cuando el paciente ingrese a su cuenta visualizará la pantalla de inicio (Fig.)




En ella se visualizan las siguientes secciones:

   -   TURNOS. Sección en la cual el paciente puede realizar tareas relacionadas con la
       gestión de turnos: visualizar históricos, tomar turnos, ver los próximos turnos
       agendados, entre otras.
   -   ESTUDIOS. Contempla tareas asociadas con la gestión de estudios: ver las
       prescripciones vigentes, acceder a resultados de estudios, visualizar imágenes.
   -   ATENCIONES. Allí puede verse un resumen de atenciones ambulatorias y eventos
       de internación. El paciente podrá solicitar un nuevo turno de una atención ya
       finalizada, en caso que aplique la configuración.
   -   E-CONSULTA. En esta sección el paciente puede visualizar los turnos vigentes en la
       modalidad de teleconsulta. También podrá recepcionarse e iniciar su atención.
   -   MEDICAMENTOS. En este módulo el usuario puede gestionar recetas de
       medicamentos: solicitarlas, imprimirlas, visualizar los detalles, entre otros.
   -   NOTIFICACIONES. En este apartado el paciente puede configurar qué medios de
       comunicación desea activar para recibir notificaciones asociadas a la gestión de sus
       turnos e informes.




                                                                                         11

   3.Configuración
En la pantalla principal, el usuario también podrá acceder a la configuración de su cuenta, y
la visualización y edición de datos personales.




En esta sección, el paciente podrá acceder a cuatro apartados:

   ●   Contraseña
   ●   Modificar datos
   ●   Notificaciones
   ●   Grupo familiar.




   -   CONTRASEÑA. Aquí el usuario puede cambiar su contraseña, para lo cual debe
       ingresar previamente su contraseña actual.




                                                                                          12

   -   MODIFICAR DATOS. En este espacio el paciente puede editar sus datos personales
       ( apellido, nombre, tipo y número de documento, sexo, fecha de nacimiento), datos
       de contacto (teléfono particular y celular) y datos filiatorios (convenio y plan).




En caso de que el paciente se encuentre en estado CONFIRMADO, solo podrá editar sus
datos de contacto y filiatorios. Si no se encontrase en dicho estado, podrá editar todos los
campos de la pantalla.

   -   NOTIFICACIONES. En este apartado el paciente puede modificar la configuración
       relacionada con qué medios de comunicación desea activar para recibir
       notificaciones.




                                                                                            13

    -   GRUPO FAMILIAR. En esta sección el paciente puede asociar personas a su grupo
        familiar. Esto es de especial utilidad para aquellos casos en los que un paciente
        desea gestionar turnos, recetas, estudios de un familiar que no puede operar su
        cuenta por medios propios.

        Además de asociar miembros al grupo familiar, puede eliminarlos o editar sus datos.
        Solo se podrá realizar esta tarea cuando el paciente no se encuentre en estado
        CONFIRMADO.




    A. Asociación de persona al grupo familiar.

Para cargar un miembro al grupo familiar, se debe seleccionar “Agregar Persona” y cargar
los datos solicitados.




Si el paciente agregado no es paciente de la institución, no tiene mail activo, o tiene un mail
activo distinto al mail de la cuenta del responsable del grupo, se cargará con tipo de
documento “Provisorio”, y un número secuencial generado automáticamente. Además, se
creará una nueva persona en estado TEMPORAL en el listado de pacientes de la
institución.




                                                                                            14

En este caso, el responsable del grupo familiar solo podrá solicitar turnos para ese paciente.
No podrá realizar ninguna otra tarea hasta que personal de la institución confirme la
vinculación responsable - miembro del grupo familiar. Si el paciente agregado ya existía, se
debe unificar las personas creadas para el paciente agregado y confirmar sus datos,
cargándole como mail activo el del responsable del grupo. Si el paciente no existía, se debe
confirmar sus datos y cargar como mail activo el del responsable del grupo.

Solo en el caso en que el paciente agregado se encuentre en estado CONFIRMADO y
tenga activo el mismo mail de la cuenta del responsable del grupo familiar, se asociará
automáticamente y la gestión de tareas en el portal por parte del responsable será
completa.




   B. Edición de datos de miembro del grupo familiar.

Es posible editar los datos de cada integrante del grupo familiar. Se respeta la misma lógica
de edición que para los datos del responsable del grupo: no se podrá modificar la
información de la persona si ya se encuentra en estado CONFIRMADO.

   C. Eliminación de miembro del grupo familiar.

En la misma pantalla de adición y modificación se ofrece la posibilidad de eliminar un
integrante del grupo familiar.




                                                                                           15

   4.Turnos
Tal como se ha mencionado, esta sección permite realizar tareas asociadas con la gestión
de turnos:

   A. Solicitar turno.
   B. Visualizar próximos turnos.
   C. Consultar el histórico de turnos.

A continuación se explicará cada una de las funcionalidades.

   A. Solicitar turno.

Para solicitar un turno se debe presionar “Solicitar turno”.




A continuación, el sistema solicitará al paciente la carga de su cobertura médica. Por default
traerá la configurada en los datos personales del paciente seleccionado:




                                                                                           16

La institución puede configurar qué convenios y qué planes estarán disponibles para que los
afiliados tomen turnos por el portal. En caso de que la cobertura no sea PRIVADO o
PARTICULAR, se habilitará la posibilidad de ingresar el número de afiliado. Si se encuentra
configurada la validación de elegibilidad, se realizará en esta instancia y permitirá al usuario
proseguir o no, dependiendo si el resultado es positivo o no, respectivamente.

Una vez seleccionada la cobertura, el paciente debe indicar si desea buscar turnos por
profesional o por especialidad. Dependiendo de la selección, debe indicar el profesional o la
especialidad correspondiente. Opcionalmente, puede filtrar por grupo de centro de atención
y/o centro de atención.




Aclaración: el concepto “especialidad” en la búsqueda de turnos hace referencia a los
“servicios” definidos por la institución.

Una vez seleccionado el profesional o la especialidad, el paciente debe indicar para qué
práctica desea tomar un turno.




                                                                                             17

El ícono de una cámara junto al nombre de la prestación indica que se trata de una
prestación cuya atención será virtual, desde el módulo de E-consultas.

En esta instancia, puede configurarse qué especialidad, profesional y prácticas se
habilitarán para que los pacientes tomen turnos desde el portal.

Una vez seleccionada la prestación, el usuario podrá visualizar la oferta de turnos, teniendo
en consideración todos los filtros aplicados.




El paciente podrá visualizar:

   ●   prestación seleccionada;
   ●   calendario del mes, con la posibilidad de seleccionar el día cuya oferta quiere ver;
   ●   listado de horarios disponibles por día, indicando hora, profesional, especialidad y
       centro de atención.

Luego de seleccionar un turno, el paciente podrá ver:

   ●   información relacionada con el turno (centro de atención, especialidad, profesional,
       prestación, fecha y hora de turno, valor del coseguro que debe abonar).
   ●   Preparación previa y documentación requerida. Esto se configura a nivel de
       prestación.




                                                                                              18

Una vez confirmado el turno, el paciente verá un resumen del turno solicitado, con la
posibilidad de imprimir esa información, tomar un nuevo turno o regresar a la pantalla de
inicio.




                                                                                      19

   B. Visualizar próximos turnos.

Se ofrece al paciente la posibilidad de visualizar los próximos turnos que tiene vigentes.




                                                                                             20

Desde este espacio el paciente puede:

   ●   imprimir la información del turno;
   ●   cancelar el turno.




   C. Consultar el histórico de turnos.

Se permite al paciente consultar por rango de fechas el histórico de turnos, con la
posibilidad de filtrar por estado de turno:

   -   cancelados, se observarán como LIBRE;
   -   concurridos, se verán como RECEPCIONADO;
   -   no concurridos, se visualizarán como OTORGADO.

Se puede imprimir un documento con información relacionada con el turno.




                                                                                21

22

   5.Estudios
Este módulo permite realizar tareas asociadas con la gestión de estudios:

   A. Visualizar las prescripciones vigentes de estudios.
   B. Visualizar el histórico de estudios.

A continuación se explicará cada una de las tareas.

   A. Visualizar prescripciones vigentes de estudios.

En esta sección se muestran todas las prescripciones vigentes realizadas por un médico en
el HIS. Se observa la fecha de pedido, la prestación y especialidad y el profesional que
realizó la prescripción. Asimismo, se brinda la posibilidad de tomar un turno a partir de la
prestación.




   B. Visualizar el histórico de estudios.

El paciente puede consultar los estudios que se le han realizado en la institución. Si el
mismo contase con imagen o informe (en estado confirmado), podrá también visualizarlos.




                                                                                         23

24

   6.Atenciones
En esta sección se muestra al paciente el histórico de sus atenciones. Se brindan filtros
para obtener información por rango de fechas, centro de atención, servicio y profesional.




Si la configuración y la disponibilidad de turnos lo permite, el paciente podrá tomar un turno
a partir de una atención ya realizada, para repetir el episodio de atención.




                                                                                            25

   7.E-consulta
En este módulo se brinda al paciente la posibilidad de gestionar tareas relacionadas con las
atenciones virtuales:

   A. Recepcionarse.

Cuando el paciente ingrese a esta sección observará los turnos de tipo teleconsulta o
virtuales que se encuentran vigentes.




La recepción del turno puede realizarse con un tiempo máximo de 1 hora de anticipación.
En caso de que aún falte para ese lapso de tiempo, el sistema informará al paciente:




En caso que el paciente deba abonar un coseguro, el sistema cuenta con la posibilidad de
realizar pagos mediante billeteras virtuales tal como Mercado Pago.


                                                                                         26

   B. Ser atendido por un profesional.

Una vez que el paciente se haya recepcionado, aparecerá en la cola de espera del servicio
o profesional correspondiente, y podrá ser llamado para que inicie la atención.




                                                                                      27

   8.Medicamentos
En este módulo el paciente podrá realizar tareas vinculadas con la gestión de recetas:

   A. Solicitar receta




Al seleccionar “Solicitar receta”, el paciente debe especificar su cobertura y, a continuación,
un centro de atención, especialidad y médico. La obligatoriedad del ingreso de especialidad
y médico es configurable.

Luego, el paciente puede buscar medicamentos de un listado de productos comerciales
(alfabeta) o escribir los ítems que desea solicitar en un campo de texto libre.




El pedido llegará a una cola de espera de pedidos, que luego será gestionado desde

   B. Visualizar el histórico de recetas




                                                                                            28

El paciente podrá acceder a todas las solicitudes de recetas realizadas desde el portal y a
todas las recetas realizadas desde atenciones médicas, con la posibilidad de ver el detalle
de cada una e imprimirla.




                                                                                        29

30

   9.Notificaciones
En este apartado el paciente puede configurar qué medios de comunicación desea activar
para recibir notificaciones.




                                                                                   31

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
