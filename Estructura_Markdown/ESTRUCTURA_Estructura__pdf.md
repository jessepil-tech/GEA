Fuente original: Estructura.pdf
Ruta original: Estructura/Estructura.pdf
Formato original: PDF

Configuración inicial
  Estructura Edilicia



               Versión 1.0

Información del Documento
                 Título del Documento         BP – Modelo de Negocios – Estructura edilicia



 Preparado por           Nombre (Empresa)             Gerencia               Rol                  Fecha




                 Bioing. Catalina Claucich        Coordinación de Analista Funcional          17/11/22
                                                  implementación



 Revisado por            Nombre (Empresa)             Gerencia               Rol                  Fecha




 Aprobado por              Nombre (Empresa)                  Proceso               Rol            Fecha




  Documento         Versión                         Motivo del Cambio                         Fecha Efectiva


   Historia      1.0.0

1. CONCEPTOS

Servicio
Se define el nombre del servicio de internación. Esto se determina en Admisión internados » Configuración »
Servicio.




Servicio Centro
Permite asociar un servicio con un centro de atención. Esto se realiza en Admisión internados »
Configuración » Servicio Centro.


Deben indicarse de forma obligatoria los parámetros de:
    •   Centro de atención al que pertenece el servicio a definir.
    •   Empresa y sucursal del servicio-centro.
    •   Si el servicio es únicamente ambulatorio, internado o de ambos.
    •   Tipo de servicio (referente a donde será dirigido el uso del servicio según el modulo a utilizarse).




Función Internación
En Admisión internados » Configuración » Función internación puede configurarse cada Función
internación, que luego podrá ser asociada a diferentes tipos de prestaciones fundamentales en los distintos
tipos de internación.
Estas prestaciones hacen referencia a:
       Prestación Pensión: prestación que es generada cada día de internación en los intervalos estipulados
        como Check In y Check Out durante el periodo de vigencia de la misma.

       ARM-INCUBADORA-LMT (Luminoterapia)-OXIDO NITRICO: prestaciones que luego de ser
        habilitadas, se gestionan para su facturación a través de la registración de una fecha de inicio y fin en
        el censo gráfico.


Existe también un parámetro de “Nursery” con el fin de que las camas vinculadas a esta función no sean
contabilizadas en el libro de internación.
Asimismo, la función puede tener asociados prestaciones o ítems con el fin de que los mismos sean
generados para su facturación cada día con el mismo mecanismo de la prestación pensión.




Tipo Internación
En Admisión internados » Configuración » Tipo Internación puede configurarse cada tipo de internación.
El sistema de hospital contempla 3 tipos de admisiones posibles:
       HOSPITALARIA
       TRANSITORIA
       AMBULATORIA


Se crea para cada una de las anteriores un tipo de internación que permita diferenciar, mediante una
asociación posterior, los siguientes parámetros para cada servicio de internación:




    1- Requiere alta médica: alta médica obligatoria para el egreso del paciente.
    2- Requiere cama: asignación obligatoria de una ocupación al realizar la admisión.
    3- Requiere domicilio: campo obligatorio al realizar la admisión.
    4- Requiere institución derivante: campo obligatorio al registrar una derivación externa.
    5- Requiere motivo internación: campo obligatorio al realizar la admisión.
    6- Requiere responsable administración: campo obligatorio al realizar la admisión.
    7- Requiere libro internación: este tipo internación genera libro de internación.

    8- Requiere diagnóstico al alta: campo obligatorio para otorgar el alta.
    9- Requiere convenio en reserva de internación: campo obligatorio ingresar reserva.
    10- Requiere epicrisis: generación del alta mediante epicrisis obligatoria.
    11- Requiere médico derivante: campo obligatorio al realizar la admisión.
    12- Requiere responsable internación: campo obligatorio al realizar la admisión.
    13- Requiere teléfono: campo obligatorio al realizar la admisión.


Tipo Internación por Servicio Centro
En Admisión internados » Configuración » Tipo de Internación por Servicio Centro se genera la asociación
del Tipo de internación con la función en cada servicio centro de internados.
Todas las prestaciones definidas en la función junto con los parámetros del tipo de internación serán
gestionados para cada paciente admisionado en ese servicio de internados.
A su vez, en esta pantalla también se puede definirse:
       Control de edad obligatorio y su rango específico: se restringe la admisión a dicho servicio si el
        paciente no se encuentra en el rango del control.
       Control sexo: se restringe la admisión a dicho servicio si el paciente no corresponde al sexo
        especificado.
       Porcentaje de reserva: parámetro que permite llevar una alerta visual en las pantallas de
        “Disponibilidad Actual” y “Reservas Internación” cuando se supera el porcentaje de reservas
        definido.
       Permite internar bebé: check que permite habilitar la internación de recién nacidos en el servicio
        (Pacientes con tipo documento nomenclado como “BEBE…”.
       Permite binomio: check que permite la internación conjunta en el servicio.


Tipo Ambiente
Aquí se definen los distintos tipos de ambientes (ocupaciones) que existen en la estructura del área de
internación. Esto se realiza en Admisión internados » Configuración » Tipo ambiente.




Sector Servicio Internación
En Admisión internados » Configuración » Sector Servicio Internación puede delimitarse por sector la
estructura del área de internación según los servicios y office-enfermería para un Centro de Atención.

Ambiente Internación
En Admisión internados » Configuración » Ambiente Internación se realiza la definición de las ocupaciones
especificas de cada servicio de internación (según función y tipo de internación).
Se le asigna: una descripción a modo de nombre, el tipo de ambiente de la ocupación, número de orden en
torno a la visualización en el censo gráfico y la cantidad de camas que contendrá el mismo.




En esta instancia solo se determina la cantidad de camas que conformarán cada ocupación.

2. CHECKLIST

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
