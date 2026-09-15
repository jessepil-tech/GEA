Fuente original: Configuración AGP.pdf
Ruta original: AGP/Configuración AGP.pdf
Formato original: PDF

             Configuración
Portal autogestión paciente
                      Versión 1.0




                                1

 INFORMACIÓN DEL DOCUMENTO

                Título del Documento      Portal de autogestión del paciente




Preparado por   Nombre (Empresa)              Gerencia           Rol                  Fecha




                Ing. Catalina Claucich        Implementación     Análisis Funcional   02/02/2022




Revisado por    Nombre (Empresa)              Gerencia           Rol                  Fecha




Aprobado por    Nombre (Empresa)                   Proceso               Rol          Fecha




Documento       Versión       Motivo del Cambio                                       Fecha Efectiva


Historia




                                                                                                   2

Información del Documento            2

Objetivos                            4

1. Configuración de notificaciones   5

2. Turnos                            6

3. Medicamentos                      9




                                     3

Objetivos

Este documento tiene como objetivo presentar la configuración específica del portal de
autogestión del paciente (AGP).




                                                                                    4

   1. Configuración de notificaciones


Cuando un paciente inicie el proceso de registro en el portal, recibirá un mail para validar su
cuenta. Ese mail puede configurarse desde Administración general >> Configuración
general >> Parámetros autogestión. Allí también puede configurarse el mail de recupero de
contraseña.




Por otro lado, es posible configurar las notificaciones que recibirá el paciente vía mail o
Whatsapp en relación a turnos y recetas. Para ello se debe acceder a Administración
general >> Configuración operativa >> Centro de atención >> Centro de atención >>
Mensajes.

Configuración de correos electrónicos

Se podrá definir el cuerpo de los mails de confirmación de turno, recordatorio de turno, turno
no asistido, cancelación de turno, confirmación de turno e-consulta, recordatorio de turno
e-consulta, pedido de receta no autorizado (cuando se rechaza el pedido de receta), pedido
de receta autorizado total y parcial, correo de receta.

Configuración de mensajes de Whatsapp

Se podrá definir el cuerpo de los mensajes de confirmación de turno, recordatorio de turno,
turno no asistido, cancelación de turno.




                                                                                             5

   2. Turnos

Convenios y planes

Es posible configurar qué convenios y qué planes se encontrarán disponibles en el portal
para que los afiliados soliciten turno. Para ello es necesario ingresar a Administración
general >> Facturación >> Convenio. Allí se debe seleccionar un convenio y luego ingresar
a Plan >> Plan y tildar la opción “Habilitado turnos web” para que el plan esté disponible en
el AGP. En caso de que no haya ningún plan del convenio visible en el AGP, no se verá el
convenio.




Prestaciones

Existe la posibilidad de configurar en la habilitación de turnos (por profesional, especialidad
o servicio) cuál será la cantidad de horas mínimas para tomar un turno web y cuál será la
cantidad de días de la agenda que se mostrarán en el portal. Para ello se debe ingresar a
Administración general >> Módulos >> Turnos >> Configuración >> Habilitación de turnos
(por profesional/especialidad o equipo).




                                                                                             6

Asimismo, puede determinarse qué prestaciones estarán disponibles en el portal para poder
tomar turnos. Para ello se debe ingresar a Administración general >> Módulos >> Turnos >>
Configuración >> Turnos (por profesional/especialidad o equipo).




Para que una prestación se visualice como una prestación de consulta virtual debe tildarse
el campo E-consulta en la configuración de la prestación en Administración general >>
Nomenclador >> Código prestación.




                                                                                        7

8

   3. Medicamentos

Definiciones generales

   -   Servicio Prescribe Web por Defecto

Se debe definir un servicio por defecto para recibir aquellos pedidos generados por el
paciente que no tengan servicio seleccionado. El paciente puede pedir para un Centro de
Atención de manera obligatoria, y puede indicar un servicio o médico que le haya realizado
anteriormente la receta.

Esta configuración permite gestionar los pedidos que no tengan servicio y se realiza en
Administración general >> Configuración operativa >> Centro atención >> Centro atención:




   -   Controla Atención en Receta Web

Otra configuración que se puede realizar en el centro es definir si al realizar el pedido, se
controlará si el paciente cuenta con una atención previa en dicho centro. Esto es para no
dejar pedir al paciente en un lugar en el cual nunca fue paciente.

   -   Lugar de Entrega de Receta Digital

Esta configuración permite configurar un mensaje que será enviado al paciente, en el caso
que la receta no se envíe adjunta por mail, indicando dónde debe retirarla y si se desea
también se le puede indicar cuándo retirarla.




                                                                                           9

Personal

   -   Servicio Centro Prescribe Vía Web

Para que un personal pueda realizar recetas vía web, se debe configurar de qué servicios
podría gestionar las mismas. Para ello se debe elegir cuáles serán los servicios y de qué
centros. Esto se realiza en la configuración del personal, en la sección “Servicio Centro
Prescribe Vía Web”:




   -   Firma Persona

Es posible cargar la imagen de la firma y sello del profesional. Esto se configura en la
pantalla del personal:




                                                                                      10

Convenios

Por cada convenio se puede parametrizar el Modo Uso de la Receta Digital, pudiendo ser
Envío mail al paciente, es decir se envía la receta con firma y sello adjunta por mail, o
Entrega Firmada al Paciente. En este último caso deberá el paciente (o un tercero) a buscar
la receta firmada, se enviará mail informando cuándo y dónde debe retirarla.

Esta configuración se realiza por convenio:




                                                                                        11

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
