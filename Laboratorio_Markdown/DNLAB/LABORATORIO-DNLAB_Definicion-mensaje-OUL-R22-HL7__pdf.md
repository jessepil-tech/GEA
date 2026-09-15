Fuente original: Definicion mensaje OUL_R22 HL7.pdf
Ruta original: Laboratorio/DNLAB/Definicion mensaje OUL_R22 HL7.pdf
Formato original: PDF

      DOCUMENTACIÓN TECNICA
DOCUMENTACIÓN ESTRUCTURA MENSAJES OUL^R22

                                                                                                             Documentacion Tecnica

                                                                                                                      Contenidos




1. Datos del documento:


Documentación
Fecha                  Autor                             Objeto
31/08/2021             Rodriguez Marcelo                 Primera emisión documento


2. Mensaje de envío de resultados:

Mensaje ejemplo:

MSH|^~\&|DNLAB|DEDALUS|SISTEMAEXTERNO| SISTEMAEXTERNO|20230331154521||OUL^R22|7275320230331154521|P|2.5
PID|||1^^^CODEST^CODEST~2^^^CS^CS~3^^^LIS^LIS~4^^^CF^CF||XXX^XXX||19301003|M|||BDO IRIGOYEN 2356^^^^^^L
PV1|1|A|AMB-SSL^^^^^^^^Ambulatorio (SSL)||||||||||||||||167470
SPM|1|580012728602||PLCIT^Plasma c/CITRATO
OBR|1||SSL230300518^DN^20235800127286|.COAG^Coagulograma|||202303310935|||||||||162920||||||||HEMOSTASIA|F||^^^202303310935
ORC|SC|SSL23030051802|SSL230300518^DN^20235800127286|SSL230300518^DN|A||||20230331164418|||162920|||||||||Ambulatorio
(SSL)^^^^^^FI^^^AMB-SSL
TQ1|1||||||||R
OBX|1|CE|.COAG^Coagulograma||TITULO|||N|||F|||20230331164012|2^HEMOSTASIA^2^0010020100|VALAUTO

Segmento MSH.
 Posición   Valor                            Tipo   Nombre                    Características
 MSH.1      MSH                                R    Field Separator           Valor fijo
 MSH.2      ^~\&                               R    Encoding Characters       Valor fijo
 MSH.3      DNLAB                              O    Sending Application       Sistema enviante
 MSH.4      DEDALUS                            O    Sending Facility          Sistema enviante
 MSH.5      SISTEMAEXTERNO                     O    Receiving Application     Valor fijo
 MSH.6      SISTEMAEXTERNO                     O    Receiving Facility        Valor fijo




Definición mensaje OUL^R22 de envio de resultados

                                                                                                                Documentacion Tecnica

                                                                                                                          Contenidos



 MSH.7     20160407115428                      R    Date/Time Of Message      Fecha (YYYYMMDDHHMISS)
 MSH.9     OUL^R22                             R    Message Type              Valor fijo
 MSH.10    7275320230331154521                 R    Message Control ID        Numero único que nunca se repite
 MSH.11    P                                   R    Processing ID             Valor fijo
 MSH.12    2.5                                 R    Version ID                Valor fijo

Segmento PID
 Posición Valor                              Tipo   Nombre                    Características
 PID.0    PID                                  R    Field Separator           Valor fijo
 PID.1                                         0    Set ID - PID              Vacio
 PID.2                                         B    Patient ID                Vacío
 PID.3    1^^^CODEST^CODEST~                   R    Patient Identifier List   Identificadores del paciente para dnlab:
          2^^^CS^CS~                                                          Posición 1→ Código externo
          3^^^LIS^LIS~                                                        Posiciones 4 y 5 → Flag identificatorio
          4^^^CF^CF                                                           CODEST – flag que identifica al paciente externo
                                                                              CS – flag que identifica la historia clínica
                                                                              CF – flag que identifica el número de documento
                                                                              LIS – flag que identifica el numero interno del lis
 PID.5     XXX^XXX                             R    Patient Name              Posición 1 → Apellido/s
                                                                              Posición 2 → Nombre/s
 PID.7     19301003                            O    Date/Time of Birth        Fecha de nacimiento en formato (YYYYMMDD)
 PID.8     M                                   O    Administrative Sex        Sexo ( F – M – N)
 PID.11    BDO IRIGOYEN 2356^^^^^^L            O    Patient Address           Posición 1 → Dirección
                                                                              Posición 7 → Valor fijo
 PID.15    S                                   O    Primary Language          Valor fijo
 PID.16    S                                   O    Marital Status            Valor fijo


Segmento PV1
 Posición Valor                              Tipo   Nombre                    Características




Definición mensaje OUL^R22 de envio de resultados

                                                                                                              Documentacion Tecnica

                                                                                                                       Contenidos



 PV1.0      PV1                                 R     Field Separator       Valor fijo
 PV1.1      1                                   0     Set ID - PV1          Valor fijo
 PV1.2      A                                   O     Patient Class         Tipo de Solicitud
 PV1.3      AMB-SSL^^^^^^^^Ambulatorio (SSL)    O     Assigned Patient      Identificadores de la ubicación del paciente.
                                                      Location              Posición 1 → servicio donde se va a enviar el informe
                                                                            “Servicio Informe”
                                                                            Posición 8 → descripción del servicio
 PV1.19     167470                              O     Visit Number          Numero de visita o episodio

Segmento SPM
 Posición   Valor                              Tipo   Nombre                Características
 SPM.0      SPM                                  R    Field Separator       Valor fijo
 SPM.1      1                                    0    Set ID - SPM          Valor fijo
 SPM.2      580012728602                         O    Specimen ID           Numero de la muestra o etiqueta
 SPM.4      PLCIT^Plasma c/CITRATO               R    Specimen Type         Posición 1→Código del tubo
                                                                            Posición 2→Descripción del tubo

Segmento ORC
 Posición   Valor                              Tipo   Nombre                Características
 ORC.0      ORC                                  R    Field Separator       Valor fijo
 ORC.1      SC                                   R    Order Control         Flag que indica la acción del mensaje:
                                                                            SC: status change
 ORC.2      SSL23030051802                      C     Placer Order Number   Nro. de solicitud externa más posición del análisis en el
                                                                            mensaje. Es un consecutivo
 ORC.3      SSL230300518^DN^20235800127286      C     Filler Order Number   Posición 1→solicitud externa
                                                                            Posición 2→DN
                                                                            Posición 3→año más número de solicitud de LIS
 ORC.4      SSL230300518^DN                     O     Placer Group Number   Posición 1→solicitud externa
                                                                            Posición 2→DN
 ORC.5      A                                   O     Order Status          Valor fijo




Definición mensaje OUL^R22 de envio de resultados

                                                                                                                  Documentacion Tecnica

                                                                                                                            Contenidos



 ORC.9      20230331164418                      O     Date/Time of Transaction   Fecha de la solicitud
 ORC.12     162920                              O     Ordering Provider          Medico: código externo y nombre
 ORC.21     Ambulatorio (SSL)^^^^^^FI^^^AMB-    O     Ordering Facility Name     Posición 1 → descripción del servicio
            SSL                                                                  Posición 7 → FI
                                                                                 Posición 10 → identificador del servicio

Segmento TQ1
 Posición   Valor                              Tipo   Nombre                     Características
 TQ1.0      TQ1                                  R    Field Separator            Valor fijo
 TQ1.1      1                                    O    Set ID - TQ1               SECUENCIA DE BLOQUE
 TQ1.9      R                                    O    Priority                   Valor del nivel de urgencia
                                                                                 R – Rutina
                                                                                 U – Urgente

Segmento OBR
 Posición   Valor                          Tipo       Nombre                     Características
 OBR.0      OBR                              R        Field Separator            Valor fijo
 OBR.1      1                                O        Set ID – OBR               SECUENCIA DE BLOQUE
 OBR.2                                       C        Placer Order Number        Vacio
 OBR.3      SSL230300518^DN^20235800127286   C        Filler Order Number        Posición 1 → numero de solicitud de LIS
                                                                                 Posición 2 → DN
                                                                                 Posición 3 → fecha solicitud
 OBR.4      COAG^Coagulograma                   R     Universal Service          Posición 1→Código del análisis
                                                      Identifier                 Posición 2→Descripción del análisis
 OBR.5      R                                   B     Priority _ OBR             Valor fijo
 OBR.6                                          B     Requested Date/Time        Vacío
 OBR.7      202303310935                        C     Observation Date/Time      Vacío
 OBR.16     162920                              O     Ordering Provider          Datos del Medico
                                                                                 Código externo
 OBR.24     HEMOSTASIA                          O     Diagnostic Serv Sect ID    sección




Definición mensaje OUL^R22 de envio de resultados

                                                                                                                Documentacion Tecnica

                                                                                                                         Contenidos



 OBR.25     F                                  C    Result Status

Segmento OBX
 Posición   Valor                            Tipo   Nombre                    Características
 OBX.0      OBX                                R    Field Separator           Valor fijo
 OBX.1      1                                  O    Set ID - OBX              Valor fijo
 OBX.2      NM                                 C    Value Type                Valor fijo
 OBX.3      COAG^Coagulograma                  R    Observation Identifier    Posición 1→Código del análisis
                                                                              Posición 2→Descripción del análisis
 OBX.4                                         C    Observation Sub-ID        Vacío
 OBX.5      TITULO                             C    Observation Value         resultado
 OBX.6                                         O    Units                     Vacío
 OBX.7                                         O    References Range          Vacío
 OBX.8      N                                  O    Abnormal Flags            Vacío
 OBX.9                                         O    Probability               Vacío
 OBX.10                                        O    Nature of Abnormal Test   Vacío
 OBX.11     F                                  R    Observation Result        Valor Fijo
                                                    Status
 OBX.12                                        O    Effective Date of         Vacío
                                                    Reference Range
 OBX.13                                        O    User Defined Access       Vacío
                                                    Checks
 OBX.14     20230331164012                     O    Date/Time of the
                                                    Observation
 OBX.15     2^HEMOSTASIA^2^0010020100          O    Producer's ID             Se usa para enviar la posición del análisis en el informe
 OBX.16     VALAUTO                            O    Responsible Observer      Usuario Validador




Definición mensaje OUL^R22 de envio de resultados

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
