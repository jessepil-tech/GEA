Fuente original: BP - Atencion Medica.pdf
Ruta original: Atención médica/BP - Atencion Medica.pdf
Formato original: PDF

BP – Modelo de Negocios


Atención Médica
Versión 1.0

 INFORMACIÓN DEL DOCUMENTO

                Título del Documento         BP – Atención Médica

Información     Localización           del
General         documento




Preparado por   Nombre (Empresa)                 Gerencia           Rol                  Fecha




                Ing. Catherine Acuña             Soporte            Análisis Funcional   15/12/2022
                                                 Funcional




Revisado por    Nombre (Empresa)                 Gerencia           Rol                  Fecha




Aprobado por    Nombre (Empresa)                     Proceso                Rol          Fecha

Documento   Versión   Motivo del Cambio   Fecha Efectiva


Historia

Índice de contenido

Información del Documento                  2
    1. Objetivos                           5
    2. Alcance                             6

1 Generalidades                            7

2 Atención Médica                           8
      2.1 Antecedentes generales           12
      2.2 Problemas y evoluciones          12
      2.3 Diagnóstico                      12
      2.4 Estudios medicamentos y dietas   12
      2.5 Anamnesis                        13
      2.6 Examen físico                    13
      2.7 Prácticas                        13
      2.8 Score                            14
      2.9 Finalizar atención               15

1. Objetivos


   En el siguiente documento se describirán los procesos asociados a la atención médica,
contemplados en el sistema Thinksoft HIS.


   Es importante aclarar que el módulo de atención médica está dividido en procesos detallados que
quedan descritos mediante su correspondiente diagrama de negocios (BPMN) en este documento.

2. Alcance
   El presente documento tiene como finalidad explicar todos los procesos y parametrización
correspondiente a la atención médica.

   En el alcance del proyecto se deberá definir la parametrización, los personal y habilitaciones
vinculados a cada servicio de atención médica.

1 GENERALIDADES

El proceso de atención médica propiamente dicho, permite hacer la atención por servicio y
por profesional. Nos permitirá visualizar el estado del paciente para la especialidad, cuánto
tiempo lleva en la espera, cuántas veces fue llamado y a que hora tiene el turno.
Además el sistema permite verificar que turnos hay para la especialidad, la historia clínica
del paciente, que pacientes fueron atendidos y también es posible visualizar que pacientes
están en atención.

El sistema diferencia la cola de espera del servicio de la del profesional y la cola de
pacientes en atención. Además se pueden visualizar los tiempos de espera en cola de
espera y el tiempo que lleva en atención el paciente.

2 A TENCIÓN MÉDICA

El módulo de atención médica contempla un único proceso que es la atención propiamente
dicha, y que es el proceso que inicia una vez que comienza la atención por parte del
médico, la cual tiene posibilidad de hacerse presencial o por E-consulta. La diferencia entre
ambos procesos antes mencionados es que al abrir la atención se inicia una videollamada
en la que participa el médico y su paciente y la atención presencial es una atención
convencional.

Para el proceso de consulta virtual, el paciente saca el turno y al mismo se le envía un link.
El paciente solo aparecerá en la cola de espera cuando haya hecho click en ese link y podrá
ingresar a la videollamada solo cuando el médico inicie la atención. Esta videollamada se
hace a través del software Jitsi, esto no es configurable.

Al iniciar la atención médica, se provee de un resumen de la historia clínica la cual es una
pantalla fija donde se visualiza:
    ● Problemas crónicos, es un valor editable por pantalla en donde se puede ingresar
         los problemas activos del paciente ya sean crónicas o agudas, reportando su fecha
         de inicio y colocando el problema que responde a la clasificación CIE.
    ● Medicación crónica
    ● Alergias, valores precargados por el usuario que pueden ser seleccionados para
         incluir en la HC.
    ● Advertencias del paciente
    ● F.U.M: es un valor editable por pantalla en donde se coloca la fecha de la última
         menstruación reportada con seguridad por la mujer que presenta ciclos menstruales
         naturales y regulares en ausencia de factores endógenos o exógenos que puedan
         modificar la fisiología del ciclo menstrual.
    ● Resumen de atenciones
    ● Estudios de laboratorio
    ● Otros estudios

Además, en la pantalla de atención médica es posible gestionar turnos. El médico puede
asignar turnos propios al paciente y puede hacer una consulta para ver disponibilidad en
otros profesionales.

En esta instancia es de gran importancia colocar el MOTIVO DE CONSULTA es un valor
configurable que puede ser o no obligatorio para finalizar la atención del paciente. Este
campo se llena con valores predefinidos pero existe la posibilidad de ingresar por teclado
algún motivo de consulta que no se encuentre allí de forma libre.

Los valores obligatorios de la Atención médica dependen de la configuración que se le de,
es decir, la atención ambulatoria puede estar configurada como:

    ●   Atención ambulatoria orientada a problemas
    ●   Atención ambulatoria reducida

Debido a esto, es importante definir antes de continuar con la lectura del documento la
diferencia entre Problema, Motivo de consulta y Diagnóstico.

El concepto de problema se incluye porque en las consultas cotidianas es frecuente no
poder tener un diagnóstico porque no se llega a detectar una entidad nosológica específica
debido a que existen problemas indiferenciados en la práctica cotidiana, es decir que el
concepto aplica a situaciones no deseadas en donde debe interferir el médico o equipo de
salud. Se considera diagnóstico, a aquello que luego de considerarse problema y
realizarse los estudios adecuados confirme un diagnóstico de enfermedad. El motivo de
consulta tiene que ver con el motivo por el cual el paciente acude al médico, puede o no
ser el problema detectado luego por el médico.


A continuación se puede observar un gráfico del Modelo de Negocios (BPMN) del proceso
de Atención Médica. Posteriormente se desarrollan los procesos en orden de importancia.

2.1 Antecedentes generales


Con respecto a los antecedentes se puede indicar desde aquí:
   ● Problemas crónicos
   ● Medicación crónica
   ● Alergias
   ● Advertencias
Además se podrán actualizar los antecedentes del paciente con formularios precargados
configurables por el usuario.

2.2 Problemas y evoluciones
Para dar evoluciones de un problema nos permite desplegar un menú con los problemas
crónicos que el paciente ya tiene cargado previamente en su HC, en caso que sean
problemas nuevos pueden agregarse.

Solo se le permite modificar la evolución del paciente a quien la ingresó, una vez finalizada
la atención médica no se puede modificar la evolución; tampoco se puede hacer rollback
con la evolución.

2.3 Diagnóstico


Para indicar el diagnóstico el sistema utiliza la clasificación CIE (Clasificación Internacional
de Enfermedades), la cual posee número de ocurrencias que pueden ser utilizadas luego
para hacer análisis estadísticos.



2.4 Estudios medicamentos y dietas


Desde este menú se puede indicar estudios medicación, guia medica, dietas, y además
permite visualizar el historial de medicamentos y estudios.
En el caso de la prescripción de estudios, se puede filtrar los últimos estudios que hayan
sido prescritos. El estudio se puede buscar por texto predictivo y existe la posibilidad de
vincularlo a un problema cuando la atención médica es orientada a problemas. Además al
seleccionar la prestación figuran los servicios que pueden realizarla, tenemos la posibilidad
de escribir una observación y el diagnóstico presuntivo.
Para prescribir medicamentos, se puede filtrar por genérico y también es posible filtrar los
medicamentos que ya hayan sido prescritos. También se puede indicar un diagnóstico
presuntivo.

Se puede indicar guía médica con recomendaciones sobre insumos, prácticas y dietas.

2.5 Anamnesis
La anamnesis es el proceso de la exploración clínica que se ejecuta mediante el
interrogatorio para identificar personalmente al individuo, se pueden completar plantillas
configuradas para el servicio en el que esté conectado el usuario.

2.6 Examen físico


El sistema permite cargar los datos de un examen exploratorio físico, se hace mediante
plantillas preconfiguradas con campo de texto libre o relleno de formularios. Cabe
mencionar que los exámenes físicos están asociados al evento de atención.

También es posible cargar datos antropométricos, ingresando los siguientes valores:

   ●   Peso (kg)
   ●   Talla (cm)
   ●    Perímetro de cintura (cm)
   ●   Perimetro decardera (cm)
   ●   Perimetro cefalico (cm)

Con estos datos se obtiene el resultado de calcular:

   ●   Área de Superficie corporal: indicador metabólico de la superficie corporal. Se
       puede elegir el método de cálculo:

           ○    Mosteller
           ○    Haycock
           ○    Du Bois & Du Bois
           ○    Gehan EA, George SL
           ○    Fórmula de Boyd

   ●   Índice de Masa Corporal
   ●   Índice Cintura / Cadera

Con respecto a los signos vitales se pueden ingresar registro de los siguientes:

   ●   Presión arterial
   ●   Frecuencia cardiaca
   ●   Frecuencia respiratoria
   ●   Temperatura
   ●   Saturación de oxígeno

Se pueden evaluar y cargar más de una vez en cada atención médica.

2.7 Prácticas
Se pueden agregar las prácticas realizadas dentro de atención médica y adicionar archivos
de imagen. Además se puede hacer un estudio con informe, modificar el informe y
confirmarlo. También el sistema permite elegir el modelo de informe y editarlo.

2.8 Score


El score aplica para los eventos de atención. Se pueden habilitar por configuración con
distintas escalas precargadas,pueden ser configuradas y habilitadas también por servicio
para ambulatorio / internación. Estas escalas se encargan de hacer una suma para devolver
el resultado interpretable para el usuario no realiza gráficos.

                 ABCD²                   GLASGOW Modificado para lactantes y niños > 1 año
               ALVARADO                       Glasgow-Blatchford Bleeding Score (GBS)
               APACHE II                                      GRACE
                 Apgar                                      HAS-BLED

         BISAP para pancreatitis                           HUNT & HESS

                 Bishop                                        ICH
                  BMI                                    Índice de Barthel
            BODE para COPD                           MARSHALL MODIFICADO


 CAM-ICU: Confusion Assessment Method
                                                              MASCC
              for the ICU


       CANADIAN CT HEAD RULE                                 MELD Na


   Categorizacion pacientes internados                        NEWS

             CHA2DS2-VASC                                     NEXUS

     Cl. Creatinina (Cockroft-Gault)                          NIHSS

      Comorbilidad de CHARLSON                               NRS 2002

        Criterios de SGARBOSSA                      PARKLAND para quemaduras

               CRUSADE                                   PESI Simplificado
                CURB-65                                 PUNTUACION NOVA

            Escala de BRADEN                          RANSON en la admisión


   Escala de Fuerza Muscular Medical
                                                RANSON 48hs después de admisión
            Research Council

            Escala de MORSE                                  ROCKALL

        ESCALA DE NORTON UPP                                   RTS

          Escala de RAIMONDI                                   SAPS II

     Escala de TAL (p/ >= de 6 meses)                          SOFA


  Escala de TAL (p/ menores de 6 meses)                  TIMI para NSTEMI

               Escala RASS                                    TINETTI

       Escala visual analógica (EVA)      TISS-28 (Therapeutic Intervention Scoring System)

            FISHER (para HSA)                                WELLS EP

                GLASGOW                                      WELLS TVP

  GLASGOW Modificado para lactantes y
           niños < 1 año




2.9 Finalizar atención
Una vez completados los campos obligatorios se puede finalizar la atención, posterior a eso
se puede imprimir:

   ●    Antecedentes
   ●    Examen Físico
   ●    Estudios
   ●    Medicamentos
   ●    Dietas y recomendaciones
   ●    Recetas
   ●    Informes
   ●    Certificado de atención
   ●    Prescripción Libre
   ●    Resumen de atención

También es posible cancelar la atención o dejar el paciente en atención.

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
