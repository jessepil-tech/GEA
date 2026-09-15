Fuente original: Definicion mensaje OML HL7.pdf
Ruta original: Laboratorio/Documentacion Laboratorio/DNLAB/Definicion mensaje OML HL7.pdf
Formato original: PDF

      DOCUMENTACIÓN TECNICA
DOCUMENTACIÓN ESTRUCTURA MENSAJES OML^021
                                                                                                      Documentacion Tecnica

                                                                                                               Contenidos




1. Datos del documento:


Documentación
Fecha                  Autor                                Objeto
14/09/2018             Rodriguez Marcelo                    Primera emisión documento


2. Mensaje de ingreso de solicitudes:


Mensaje ejemplo:

MSH|^~\&|GENERICO|GENERICO|DNLAB|DIANOEMA|20160407115428||OML^O21^OML_O21|1460040867320|P|2.5
PID|1||50546^^^CODEST^CODEST~64849^^^CS^CS~4064825^^^CF^CF||XXX^XXX XXX||19301003|M|||VIRREY DEL PINO N° 1736 PISO 16 DTO
A^^^||4783-5856 1536927843^PRN~a@hotmail.com^NET||S|S
PV1|1|H|F5103^^4006|||||||||||||||A|167470
ORC|NW|hce14600408673201||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428||
||N2_LABOR^001
TQ1|01||||||||N
OBR|01|hce14600408673201||GLU^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
OBX|1|ST|11536-0 || Esta es una nota a la solicitud||||||F|||

Segmento MSH.
 Posición   Valor                             Tipo     Nombre                    Características
 MSH.1      MSH                                 R      Field Separator           Valor fijo
 MSH.2      ^~\&                                R      Encoding Characters       Valor fijo
 MSH.3      GENERICO                            O      Sending Application       Sistema enviante
 MSH.4      GENERICO                            O      Sending Facility          Sistema enviante




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                       Documentacion Tecnica

                                                                                                                                Contenidos



 MSH.5      DNLAB                               O      Receiving Application        Valor fijo
 MSH.6      DIANOEMA                            O      Receiving Facility           Valor fijo
 MSH.7      20160407115428                      R      Date/Time Of Message         Fecha (YYYYMMDDHHMISS)
 MSH.8                                          O      Security                     Vacío
 MSH.9      OML^O21^OML_O21                     R      Message Type                 Valor fijo
 MSH.10     hce1460040867320                    R      Message Control ID           Numero único que nunca se repite
 MSH.11     P                                   R      Processing ID                Valor fijo
 MSH.12     2.5                                 R      Version ID                   Valor fijo

Segmento PID
 Posición   Valor                             Tipo     Nombre                       Características
 PID.0      PID                                 R      Field Separator              Valor fijo
 PID.1      1                                   0      Set ID - PID                 Valor fijo
 PID.2                                          B      Patient ID                   Vacío
 PID.3      1^^^CODEST^CODEST~                  R      Patient Identifier List      Identificadores del paciente para dnlab:
            2^^^CS^CS~                                                              Posición 1: Código externo
            3^^^CF^CF                                                               Posiciones 4 y 5: Flag identificatorio
                                                                                    CODEST – flag que identifica al paciente externo
                                                                                    CS – flag que identifica la historia clínica
                                                                                    CF – flag que identifica el número de documento
 PID.4                                          B      Alternate Patient ID - PID   Vacío
 PID.5      XXX^XXX                             R      Patient Name                 Primer posición Apellido, segunda posición nombre/s
 PID.6                                          O      Mother's Maiden Name         Apellido Materno o segundo apellido
 PID.7      19301003                            O      Date/Time of Birth           Fecha de nacimiento en formato (YYYYMMDD)
 PID.8      M                                   O      Administrative Sex           Sexo ( F – M – N)
 PID.9                                          B      Patient Alias                Vacío
 PID.10                                         O      Race                         Vacío
 PID.11     VIRREY DEL PINO N°                  O      Patient Address              PID.11^1 – Dirección
            1736^^^^1402                                                            PID.11^5 – Código Postal
 PID.12                                         O      County Code                  Vacio




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                      Documentacion Tecnica

                                                                                                                                Contenidos



 PID.13     4783-                               O      Phone Number - Home         Datos de contacto:
            5856^PRN~a@hotmail.com^NET                                             PRN – teléfono
                                                                                   NET – email
 PID.14                                         O      Phone Number - Business     Vacio
 PID.15     S                                   O      Primary Language            Valor fijo
 PID.16     S                                   O      Marital Status              Valor fijo


Segmento PV1
 Posición   Valor                             Tipo     Nombre                      Características
 PV1.0      PV1                                 R      Field Separator             Valor fijo
 PV1.1      1                                   0      Set ID - PV1                Valor fijo
 PV1.2      H                                   O      Patient Class               Tipo de Solicitud
                                                                                   C – Ambulatorio (0)
                                                                                   U – Guardia (1)
                                                                                   H – Internado (2)
                                                                                   M – Hospital de Dia (3)
 PV1.3      GENERICO^^^CAMA                     O      Assigned Patient Location   Identificadores de la ubicación del paciente.
                                                                                   PV1.3^0 – Es el servicio donde se encuentra ubicado el
                                                                                   paciente, en el caso de la interface GENERICA es el cliente
                                                                                   que envía la orden.
                                                                                   PV1.3^3 – en el caso de que sea un internado se utiliza
                                                                                   para enviar la cama donde está el paciente
 PV1.4                                          O      Admission Type              Vacio
 PV1.5                                          O      Preadmit Number             Vacio
 PV1.6                                          O      Prior Patient Location      Vacio
 PV1.7                                          O      Attending Doctor            Vacio
 PV1.8                                          O      Referring Doctor            Vacio
 PV1.9                                          B      Consulting Doctor           Vacio
 PV1.10                                         O      Hospital Service            Vacio
 PV1.11                                         O      Temporary Location          Vacio




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                      Documentacion Tecnica

                                                                                                                                Contenidos



 PV1.12                                         O      Preadmit Test Indicator    Vacio
 PV1.13                                         O      Re-admission Indicator     Vacio
 PV1.14                                         O      Admit Source               Vacio
 PV1.15                                         O      Ambulatory Status          Vacio
 PV1.16                                         O      VIP Indicator              Vacio
 PV1.17                                         O      Admitting Doctor           Vacio
 PV1.18     A                                   O      Patient Type               Valor fijo
 PV1.19     167470                              O      Visit Number               Numero de visita o episodio


Segmento ORC
 Posición   Valor                             Tipo     Nombre                     Características
 ORC.0      ORC                                 R      Field Separator            Valor fijo
 ORC.1      NW                                  R      Order Control              Flag que indica la acción del mensaje:
                                                                                  NW: crear
                                                                                  RP: reemplazar
                                                                                  CA: borrar o cancelar
 ORC.2      hce14600408673201                   C      Placer Order Number        Nro. de solicitud externa más posición del análisis en el
                                                                                  mensaje. Es un consecutivo
 ORC.3                                          C      Filler Order Number        Vacío
 ORC.4      hce146004086732                     O      Placer Group Number        Nro. de solicitud externa
 ORC.5      IP                                  O      Order Status               Valor fijo
 ORC.6                                          O      Response Flag              Vacío
 ORC.7                                          B      Quantity/Timing            Vacío
 ORC.8                                          O      Parent Order               Vacío
 ORC.9      20160408060000                      O      Date/Time of Transaction   Fecha de la solicitud
 ORC.10                                         O      Entered By                 Vacío
 ORC.11                                         O      Verified By                Vacío
 ORC.12     2000000000515A2F^                   O      Ordering Provider          Medico: código externo y nombre
            Cabañuz^Florencia




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                     Documentacion Tecnica

                                                                                                                              Contenidos



 ORC.13                                         O      Enterer's Location          Vacío
 ORC.14                                         O      Call Back Phone Number      Vacío
 ORC.15     20160407115428                      O      Order Effective Date/Time   Fecha de extracción
 ORC.16                                         O      Order Control Code          Vacío
                                                       Reason
 ORC.17                                         O      Entering Organization       Vacío
 ORC.18                                         O      Entering Device             Vacío
 ORC.19     N2_LABOR^001                        O      Action By                   Valor Fijo

Segmento TQ1
 Posición   Valor                             Tipo     Nombre                      Características
 TQ1.0      TQ1                                 R      Field Separator             Valor fijo
 TQ1.1      01                                  O      Set ID - TQ1                SECUENCIA DE BLOQUE
 TQ1.2                                          O      Quantity                    Valor fijo
 TQ1.3                                          O      Repeat Pattern              Vacío
 TQ1.4                                          O      Explicit Time               Vacío
 TQ1.5                                          O      Relative Time and Units     Vacío
 TQ1.6                                          O      Service Duration            Vacío
 TQ1.7                                          O      Start date/time             Vacío
 TQ1.8                                          O      End date/time               Vacío
 TQ1.9      N                                   O      Priority                    Valor del nivel de urgencia
                                                                                   R – Rutina (0)
                                                                                   U – Urgente (3)
                                                                                   Default si envían cualquier código desconocido para
                                                                                   DNLab: R

Segmento OBR
 Posición   Valor                             Tipo     Nombre                      Características
 OBR.0      OBR                                 R      Field Separator             Valor fijo
 OBR.1      01                                  O      Set ID – OBR                SECUENCIA DE BLOQUE




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                           Documentacion Tecnica

                                                                                                                                    Contenidos



 OBR.2      hce14600408673201                   C      Placer Order Number            Nro. de solicitud externa más posición del análisis en el
                                                                                      mensaje. Es un consecutivo
 OBR.3                                          C      Filler Order Number            Vacío
 OBR.4      GLU                                 R      Universal Service Identifier   Código de análisis
 OBR.5      R                                   B      Priority _ OBR                 Valor fijo
 OBR.6                                          B      Requested Date/Time            Vacío
 OBR.7                                          C      Observation Date/Time          Vacío
 OBR.8                                          O      Observation End                Vacío
                                                       Date/Time
 OBR.9                                          O      Collection Volume              Vacío
 OBR.10                                         O      Collector Identifier           Vacío
 OBR.11                                         O      Specimen Action Code           Vacío
 OBR.12                                         O      Danger Code                    Vacío
 OBR.13                                         O      Relevant Clinical              Vacío
                                                       Information
 OBR.14                                         B      Specimen Received              Vacío
                                                       Date/Time
 OBR.15                                         B      Specimen Source                Vacío
 OBR.16     2000000000515A2F^                   O      Ordering Provider              Datos del Medico
            Cabañuz^Florencia                                                         Código externo
                                                                                      Apellido
                                                                                      Nombre

Segmento OBX
 Posición   Valor                             Tipo     Nombre                         Características
 OBX.0      OBX                                 R      Field Separator                Valor fijo
 OBX.1      1                                   O      Set ID - OBX                   Valor fijo
 OBX.2      NM                                  C      Value Type                     Valor fijo
 OBX.3      11536-0                             R      Observation Identifier         Parámetros fijos pre establecidos:




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                      Documentacion Tecnica

                                                                                                                                Contenidos



                                                                                   11539-0^MOTDESC^LN (flag que indica que se va a
                                                                                   enviar el diagnostico presuntivo de texto libre)
                                                                                   19153-6(flag que indica que se va a enviar la cantidad para
                                                                                   la diuresis)
                                                                                   11536-0 (flag que indica que se va a enviar una nota a la
                                                                                   solicitud)
                                                                                   8335-2 (flag que indica que se va a enviar el Peso)
                                                                                   8301-4 (flag que indica que se va a enviar la Altura)
                                                                                   11540-0^ENTE^LN (flag que indica que se va a enviar el
                                                                                   ente al que se va a facturar de parte de Centralab)
                                                                                   11542-0^INSURANCEAFI^LN ( flag que identifica que se
                                                                                   va a enviar el número de afiliado del paciente)
                                                                                   11542-1^INSURANCEOSI^LN ( flag que identifica que se
                                                                                   va a enviar el identificador de la Obra social del paciente)
                                                                                   11542-2^INSURANCEOSD^LN ( flag que identifica que se
                                                                                   va a enviar la descripción de la Obra social del paciente)
 OBX.4                                          C      Observation Sub-ID          Vacío
 OBX.5      diagnostico por código              C      Observation Value           Descripción relacionada al OBX.3
 OBX.6                                          O      Units                       Vacío
 OBX.7                                          O      References Range            Vacío
 OBX.8                                          O      Abnormal Flags              Vacío
 OBX.9                                          O      Probability                 Vacío
 OBX.10                                         O      Nature of Abnormal Test     Vacío
 OBX.11     F                                   R      Observation Result Status   Valor Fijo
 OBX.12                                         O      Effective Date of           Vacío
                                                       Reference Range
 OBX.13                                         O      User Defined Access         Vacío
                                                       Checks


Ejemplos:




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                       Documentacion Tecnica

                                                                                                                                 Contenidos



     Se marcan a modo de visualización los datos mas significativos en la mensajería. El mismo es un mensaje modelo que se envía desde OMINT
y tiene la estructura requerida.

   Ingreso de solicitud:

MSH|^~\&|GENERICO|GENERICO|DNLAB|DIANOEMA|20160407115428||OML^O21^OML_O21|1460040867320|P|2.5
PID|1||50546^^^CODEST^CODEST~64849^^^CS^CS~4064825^^^CF^CF||XXX^XXX XXX||19301003|M|||VIRREY DEL PINO N° 1736 PISO 16 DTO
A^^^||4783-5856 1536927843^PRN~a@hotmail.com^NET||S|S
PV1|1|H|F5103^^4006|||||||||||||||A|167470
ORC|NW|hce14600408673201||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428||
||N2_LABOR^001
TQ1|01||||||||N
OBR|01|hce14600408673201||GLU^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
ORC|NW|hce14600408673202||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428||
||N2_LABOR^001
TQ1|02||||||||N
OBR|02|hce14600408673202||URE^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
OBX|1|ST|11539-0^MOTDESC^LN|| Este es un diagnostico descriptivo a la solicitud||||||F|||
OBX|2|ST|11540-0^ENTE^LN || 204CLE||||||F|||
OBX|3|ST|11536-0 || Esta es una nota a la solicitud||||||F|||
OBX|4|ST|11542-0^INSURANCEAFI^LN||AB34522332||||||F
OBX|5|ST|11542-1^INSURANCEOSI^LN||102||||||F
OBX|6|ST|11542-2^INSURANCEOSD^LN||O.S.D.E||||||F

  Anulación de un análisis de la solicitud:

MSH|^~\&|GENERICO|GENERICO|DNLAB|DIANOEMA|20160407115428||OML^O21^OML_O21|1460040867321|P|2.5
PID|1||50546^^^CODEST^CODEST~64849^^^CS^CS~4064825^^^CF^CF||XXX^XXX XXX||19301003|M|||VIRREY DEL PINO N° 1736 PISO 16 DTO
A^^^||4783-5856 1536927843^PRN~a@hotmail.com^NET||S|S
PV1|1|H|F5103^^4006|||||||||||||||A|167470
ORC|CA|hce14600408673201||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428|||
|N2_LABOR^001
TQ1|01||||||||N




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                            Documentacion Tecnica

                                                                                                                                        Contenidos



OBR|01|hce14600408673201||GLU^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
OBX|1|ST|11539-0^MOTDESC^LN|| Este es un diagnostico descriptivo a la solicitud||||||F|||
OBX|2|ST|11540-0^ENTE^LN || 204CLE||||||F|||
OBX|3|ST|11536-0 || Esta es una nota a la solicitud||||||F|||


   Anulación de todos los análisis de la solicitud, como consecuencia se borra la solicitud ya que no tiene ningún análisis asociado:

MSH|^~\&|GENERICO|GENERICO|DNLAB|DIANOEMA|20160407115428||OML^O21^OML_O21|1460040867322|P|2.5
PID|1||50546^^^CODEST^CODEST~64849^^^CS^CS~4064825^^^CF^CF||XXX^XXX XXX||19301003|M|||VIRREY DEL PINO N° 1736 PISO 16 DTO
A^^^||4783-5856 1536927843^PRN~a@hotmail.com^NET||S|S
PV1|1|H|F5103^^4006|||||||||||||||A|167470
ORC|CA|hce14600408673201||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428|||
|N2_LABOR^001
TQ1|01||||||||N
OBR|01|hce14600408673201||GLU^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
ORC|CA|hce14600408673202||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428|||
|N2_LABOR^001
TQ1|02||||||||N
OBR|02|hce14600408673202||URE^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
OBX|1|ST|11539-0^MOTDESC^LN|| Este es un diagnostico descriptivo a la solicitud||||||F|||
OBX|2|ST|11540-0^ENTE^LN || 204CLE||||||F|||
OBX|3|ST|11536-0 || Esta es una nota a la solicitud||||||F|||


Notas:

   •     - Los nuevos clientes se cargan en DNLab como servicio. Si se necesita que cada nuevo cliente tenga que tener una numeración diferente,
         se tiene que configurar un punto de ingreso y luego en el DBFManager“hay que hacer una transcodificación del servicio y asociarle el
         Punto de ingreso que se desee.
   •     - El ente facturable de centralab que se va a enviar por mensajería se tiene que configurar como ente en dnlab.




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                Documentacion Tecnica

                                                                                                         Contenidos



3. Mensaje de respuesta al envió de solicitudes:
Mensaje de envió de ejemplo:

MSH|^~\{|GENERICO|GENERICO|DNLAB|DIANOEMA|20180813102418|K000019|OML^O21^OML_O21|A|D|2.5|||NE|AL|ARG
PID|1||1241528^^^CS^CS~1241528^^^CODEST^CODEST~4156^^^CF^CF||PEPE GRILLO^||19861231|M
PV1||U|F1634^^-||||||||||||||||-||||||||2
ORC|NW|K3001212511||K300121251|||||20180813102546|||VALMA^ALMARZA^VERONICA^^^^^^SGH|||20180813102546
TQ1|1||||||20180813102546||U
OBR|1|K3001212511||COL||||||||||||||174-0||||||||
ORC|NW|K3001212512||K300121251|||||20180813102546|||VALMA^ALMARZA^VERONICA^^^^^^SGH|||20180813102546
TQ1|2||||||20180813102546||U
OBR|2|K3001212512||AFIN||||||||||||||413-0||||||||
ORC|NW|K3001212513||K300121251|||||20180813102546|||VALMA^ALMARZA^VERONICA^^^^^^SGH|||20180813102546
TQ1|3||||||20180813102546||U
OBR|3|K3001212513||IONO||||||||||||||546-0||||||||
OBX|3|ST|11540-0^ENTE^LN||1634FRE||||||F

Mensaje ACK de respuesta sin errores:

MSH|^~\&amp;|DNLAB|DIANOEMA|GENERICO|GENERICO|20180824110416||ORL^O22^ORL_O22|HL7Gtw01656C4038D300|P|2.5||||||885
9/1
MSA|AA|A
PID|1||1241528^^^CS^CS~1241528^^^CODEST^CODEST~4156^^^CF^CF||PEPE GRILLO||19861231|M
ORC|OK|K3001212511|160029146520180813102510^DN|K300121251|||||20180813102546
TQ1|||||||||U
OBR||K3001212511|160029146520180813102510^DN|COL
SPM|1|160029146501||SUE|||||||||||||20180813102500|||||||||1
ORC|OK|K3001212513|160029146520180813102530^DN|K300121251|||||20180813102546
TQ1|||||||||U
OBR||K3001212513|160029146520180813102530^DN|IONO
SPM|1|160029146501||SUE|||||||||||||20180813102500|||||||||1
ORC|OK|K3001212512|160029146520180813102520^DN|K300121251|||||20180813102546
TQ1|||||||||U




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                  Documentacion Tecnica

                                                                                                                           Contenidos



OBR||K3001212512|160029146520180813102520^DN|AFIN
SPM|1|160029146502||SUE|||||||||||||20180813102500|||||||||1


Segmento MSH.
 Posición   Valor                             Tipo     Nombre                  Características
 MSH.0      MSH                                 R      Field Separator         Valor fijo
 MSH.1      ^~\&                                R      Encoding Characters     Valor fijo
 MSH.2      DNLAB                               O      Sending Application     Sistema enviante
 MSH.3      DIANOEMA                            O      Sending Facility        Sistema enviante
 MSH.4      GENERICO                            O      Receiving Application   Valor fijo
 MSH.5      GENERICO                            O      Receiving Facility      Valor fijo
 MSH.6      20180824110416                      R      Date/Time Of Message    Fecha (YYYYMMDDHHMISS)
 MSH.7                                          O      Security                Vacío
 MSH.8      ORL^O22^ORL_O22                     R      Message Type            Valor fijo
 MSH.9      HL7Gtw01656C4038D300                R      Message Control ID      Numero único que nunca se repite
 MSH.10     P                                   R      Processing ID           Valor fijo
 MSH.11     2.5                                 R      Version ID              Valor fijo
 MSH.12                                         O      Sequence Number         Vacío
 MSH.13                                         O      Continuation Pointer    Vacío
 MSH.14                                         O      Accept Acknowledgment   Vacío
                                                       Type
 MSH.15                                         O      Application             Vacío
                                                       Acknowledgment Type
 MSH.16                                         O      Country Code            Vacío
 MSH.17     8859/1                              O      Character Set           Valor fijo

Segmento MSA.
 Posición   Valor                             Tipo     Nombre                  Características
 MSA.0      MSA                                 R      Field Separator         Valor fijo




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                       Documentacion Tecnica

                                                                                                                                Contenidos



 MSA.1      AA                                  R      Acknowledgment Code          Valor fijo AA – AE
 MSA.2      A                                   R      Message Control ID           Identificador del mensaje al que se responde
 MSA.3                                          B      Text Message
 MSA.4                                          O      Expected Sequence
                                                       Number
 MSA.5                                          W      Delayed Acknowledgment
                                                       Type
 MSH.6                                          B      Error Condition

Segmento PID
 Posición   Valor                             Tipo     Nombre                       Características
 PID.0      PID                                 R      Field Separator              Valor fijo
 PID.1      1                                   0      Set ID - PID                 Valor fijo
 PID.2                                          B      Patient ID                   Vacío
 PID.3      1241528^^^CS^CS~                    R      Patient Identifier List      Identificadores del paciente para dnlab:
            1241528^^^CODEST^CODEST~                                                CODEST – paciente externo
            4156^^^CF^CF                                                            CS – nro. de historia clínica
                                                                                    CF – nro. de documento
 PID.4                                          B      Alternate Patient ID - PID   Vacío
 PID.5      PEPE GRILLO                         R      Patient Name                 Apellido
 PID.6                                          O      Mother's Maiden Name         Apellido Materno o segundo apellido
 PID.7      19861231                            O      Date/Time of Birth           Fecha de nacimiento en formato (YYYYMMDD)
 PID.8      M                                   O      Administrative Sex           Sexo ( F – M – N)
 PID.9                                          B      Patient Alias                Vacío
 PID.10                                         O      Race                         Vacío
 PID.11     VIRREY DEL PINO N° 1736             O      Patient Address              Dirección
 PID.12                                         O      County Code                  Se envia el código del cliente que envio el mensaje
 PID.13                                                Phone Number - Home          Datos de contacto:
                                                                                    PRN – teléfono
                                                                                    NET – email




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                      Documentacion Tecnica

                                                                                                                               Contenidos



 PID.14                                         O      Phone Number - Business    Vacio
 PID.15                                         O      Primary Language           Valor fijo
 PID.16                                         O      Marital Status             Valor fijo


Segmento ORC
 Posición   Valor                             Tipo     Nombre                     Características
 ORC.0      ORC                                 R      Field Separator            Valor fijo
 ORC.1      OK                                  R      Order Control              Flag que indica la acción del mensaje:
                                                                                  OK: ingreso correcto del análisis
                                                                                  UA: error en el ingreso del análisis
 ORC.2      K3001212511                         C      Placer Order Number        Nro. de solicitud externa más posición del análisis en el
                                                                                  mensaje. Es un consecutivo
 ORC.3      160029146520180813102510^DN         C      Filler Order Number        Valor del Lis al ingreso: numero de solicitud + fecha de
                                                                                  ingreso (YYYYMMDDHHMISS)
 ORC.4      K300121251                          O      Placer Group Number        Nro. de solicitud externa
 ORC.5                                          O      Order Status               Valor fijo
 ORC.6                                          O      Response Flag              Vacío
 ORC.7                                          B      Quantity/Timing            Vacío
 ORC.8                                          O      Parent Order               Vacío
 ORC.9      20180813102546                      O      Date/Time of Transaction   Fecha de la solicitud (YYYYMMDDHHMISS)


Segmento TQ1
 Posición   Valor                             Tipo     Nombre                     Características
 TQ1.0      TQ1                                 R      Field Separator            Valor fijo
 TQ1.1                                          O      Set ID - TQ1               SECUENCIA DE BLOQUE
 TQ1.2                                          O      Quantity                   Valor fijo
 TQ1.3                                          O      Repeat Pattern             Vacío
 TQ1.4                                          O      Explicit Time              Vacío




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                          Documentacion Tecnica

                                                                                                                                   Contenidos



 TQ1.5                                          O      Relative Time and Units        Vacío
 TQ1.6                                          O      Service Duration               Vacío
 TQ1.7                                          O      Start date/time                Vacío
 TQ1.8                                          O      End date/time                  Vacío
 TQ1.9      U                                   O      Priority                       Valor del nivel de urgencia
                                                                                      R – Rutina (0)
                                                                                      U – Urgente (3)
                                                                                      Default si envían cualquier código desconocido para
                                                                                      DNLab: R


Segmento OBR
 Posición   Valor                             Tipo     Nombre                         Características
 OBR.0      OBR                                 R      Field Separator                Valor fijo
 OBR.1                                          O      Set ID – OBR                   SECUENCIA DE BLOQUE
 OBR.2      K3001212511                         C      Placer Order Number            Nro. de solicitud externa más posición del análisis en el
                                                                                      mensaje. Es un consecutivo
 OBR.3      160029146520180813102510^DN         C      Filler Order Number            Valor del Lis al ingreso: número de solicitud + fecha de
                                                                                      ingreso (YYYYMMDDHHMISS)
 OBR.4      COL                                 R      Universal Service Identifier   Código de análisis


Segmento SPM
 Posición   Valor                             Tipo     Nombre                         Características
 SPM.0      SPM                                 R      Field Separator                Valor fijo
 SPM.1      1                                   O      Set ID _ SPM                   SECUENCIA DE BLOQUE
 SPM.2      160029146501                        O      Specimen ID                    Nro. de solicitud externa más posición del análisis en el
                                                                                      mensaje. Es un consecutivo
 SPM.3                                          O      Specimen Parent IDs            Valor del Lis al ingreso: número de solicitud + fecha de
                                                                                      ingreso
 SPM.4      SUE                                 R      Specimen Type                  Código de material de la muestra




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                 Documentacion Tecnica

                                                                                                                          Contenidos



 SPM.5                                          O      Specimen Type Modifier     Vacío
 SPM.6                                          O      Specimen Additives         Vacío
 SPM.7                                          O      Specimen Collection        Vacío
                                                       Method
 SPM.8                                          O      Specimen Source Site       Vacío
 SPM.9                                          O      Specimen Source Site       Vacío
                                                       Modifier
 SPM.10                                         O      Specimen Collection Site   Vacío
 SPM.11                                         O      Specimen Role              Vacío
 SPM.12                                         O      Specimen Collection        Vacío
                                                       Amount
 SPM.13                                         C      Grouped Specimen Count     Vacío
 SPM.14                                         O      Specimen Description       Vacío
 SPM.15                                         O      Specimen Handling Code     Vacío
 SPM.16                                         O      Specimen Risk Code         Vacío
 SPM.17    20180813102500                       O      Specimen Collection        Fecha de ingreso de la solicitud (YYYYMMDDHHMISS)
                                                       Date/Time
 SPM.18                                         O      Specimen Received          Vacío
                                                       Date/Time
 SPM.19                                         O      Specimen Expiration        Vacío
                                                       Date/Time
 SPM.20                                         O      Specimen Availability      Vacío
 SPM.21                                         O      Specimen Reject Reason     Vacío
 SPM.22                                         O      Specimen Quality           Vacío
 SPM.23                                         O      Specimen                   Vacío
                                                       Appropriateness
 SPM.24                                         O      Specimen Condition         Vacío
 SPM.25                                         O      Specimen Current           Vacío
                                                       Quantity




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                     Documentacion Tecnica

                                                                                                                              Contenidos



 SPM.26    1                                    O      Number of Specimen        Indica la cantidad de etiquetas que se debe imprimir
                                                       Containers

Mensaje de envió de ejemplo:

MSH|^~\&|MED|MED|DNLAB|DIANOEMA|20180831125449||OML^O21^OML_O21|PIC50f0a0fd09cecca2|P|2.5||||||8859/1
PID|||||^^^^^^L
PV1|1|H|^^-
ORC|NW|155449501||1554495|||||20180831125449||||5R01||20180831125449
TQ1|01||||||||U
OBR|01|155449501||617^HDL-COLESTEROL|U|||||||||||MED
SPM|5R01|75554495R01||AUX|||||||||||||20180831125449
ORC|NW|155449502||1554495|||||20180831125449||||5R01||20180831125449
TQ1|02||||||||U
OBR|02|155449502||174^COLESTEROL TOTAL|U|||||||||||MED
SPM|5R01|75554495R01||AUX|||||||||||||20180831125449
ORC|NW|155449503||1554495|||||20180831125449||||5R01||20180831125449
TQ1|03||||||||U
OBR|03|155449503||174^COLESTEROL TOTAL|U|||||||||||MED
OBX|1|ST|11536-0||||||||F
OBX|3|ST|11540-0^ENTE^LN||201FRE||||||F
SPM|5R01|75554495R01||AUX|||||||||||||20180831125449


Mensaje ACK de respuesta de mensaje con errores:

MSH|^~\&|LIS|NOEMALIFE|MED|MED|20180831175525.8750+0100||ACK|HL7Gtw016590B2413300|P|2.5
MSA|AE|PIC50f0a0fd09cecca2|java.sql.SQLException: ORA-01400: cannot insert NULL into
("HL7LAB"."TBLHL7SEGPV1"."STRPATIENTLIST")\.sp\ORA-06512: at "HL7LAB.HL7LABPROCS", line 674\.sp\ORA-06512: at line 1

Segmento MSA.
 Posición Valor                                                    Tipo     Nombre                 Características




Definición mensaje OML^021 de ingreso de solicitudes
                                                                                                                            Documentacion Tecnica

                                                                                                                                     Contenidos



 MSA.0       MSA                                                           R     Field Separator           Valor fijo
 MSA.1       AA                                                            R     Acknowledgment            Valor fijo AA – AE
                                                                                 Code
 MSA.2       PIC50f0a0fd09cecca2                                           R     Message Control ID        Identificador del mensaje al que se
                                                                                                           responde
 MSA.3       java.sql.SQLException: ORA-01400: cannot insert NULL          B     Text Message              Descripción del error ocurrido en el LIS
             into
             ("HL7LAB"."TBLHL7SEGPV1"."STRPATIENTLIST")\.sp\ORA-
             06512: at "HL7LAB.HL7LABPROCS", line 674\.sp\ORA-
             06512: at line 1
 MSA.4                                                                     O     Expected Sequence
                                                                                 Number
 MSA.5                                                                    W      Delayed
                                                                                 Acknowledgment Type
 MSH.6                                                                     B     Error Condition

Notas:

   •     - El error que se ve en el AE es porque los datos del segmento de la visita del paciente están incompletos o mal formado.




Definición mensaje OML^021 de ingreso de solicitudes


[ELEMENTO VISUAL NO CONVERTIDO: imagen]
