Fuente original: Definicion mensaje MDM_T02 HL7 (1).pdf
Ruta original: Laboratorio/DNLAB/Definicion mensaje MDM_T02 HL7 (1).pdf
Formato original: PDF

      DOCUMENTACIÓN TECNICA
DOCUMENTACIÓN ESTRUCTURA MENSAJES MDM^T02

                                                                                                                   Documentacion Tecnica

                                                                                                                            Contenidos




1. Datos del documento:


Documentación
Fecha                 Autor                             Objeto
31/08/2021            Rodriguez Marcelo                 Primera emisión documento
18/04/2023            Rodriguez Marcelo                 Cambio del estado del informe que se envia en el mensaje

2. Mensaje de envío de informe:

Mensaje ejemplo:
MSH|^~\&|DNLAB|DEDALUS|SISTEMAEXTERNO|SISTEMAEXTERNO|20230404131845||MDM^T02^MDM_T02|7288720230404131845|P|2.5
EVN|T02|20230404141842||||20230404141842
PID|||1105582^^^CODEST^CODEST~2345699^^^CF^CF~2345699^^^CS^CS~5800102851^^^LIS^LIS||XXX^XXXXXX||19381021|F|||GARIBALDI
355^^999BAI^^^^L
PV1|1|U|INT-SSL^^^^^^^^Piso (SSL)||||||||||||||||167470
TXA|1|LD|PDF|20230403174600||20230404141820||||||17219958||4842|5800127893||F
OBX||ED|DOCUMENTO||^multipart^ZPDF^Base64^JVBERi0xLjIgCiXi48/TIAoxIDAgb2JqIAo8PCAKL1R5cGUgL0NhdGFsb2cgCi9QYWdlcyAyIDAgUi
AKL1BhZ2VNb2RlIC9Vc2VOb25lIAovVmlld2VyUHJlZmVyZW5jZXMgPDwgCi9GaXRXaW5kb3cgdHJ1ZSAKL1BhZ2VMYXlvdXQgL1NpbmdsZVBhZ2UgCi9Ob2
5GdWxsU2NyZWVuUGFnZU1vZGUgL1VzZU5vbmUgCj4+IAo+PiAKZW5kb2JqIAoyIDAgb2JqIAo8PCAKL1R5cGUgL1BhZ2VzIAovS2lkcyBbIDggMCBSIF0gCi
9Db3VudCAxIAovTWVkaWFCb3ggMyAwIFIgCi9Dcm9wQm94IDQgMCBSIAo+PiAKZW5kb2JqIAozIDAgb2JqIApbIDAgMCA2MTIgNzkyIF0gCmVuZG9iaiAKNC
AwIG9iaiAKWyAwIDAgNjEyIDc5MiBdIAplbmRvYmogCjUgMCBvYmogCjw8IAovTGVuZ3RoIDI1NDYgCi9GaWx0ZXIgWyAvRmxhdGVEZWNvZGUgXSAKPj4gCn
N0cmVhbQp4nK2ayXbTyBrH936KWsI5xKlRQ1bXJAHCSSDEpu8mm4osG/XRAJINdD/afZtesugVL3C/GiSVLAkcO02fnPonn8q/+oaaZDzFAmH1r25E2eT0Iv
6aRPHd65coqlx1Pp9Q9HbyBU2YhxHBfihQNiHEb1TaKMxDUMxpf5r8d5Krh+GzsP7E5uf8fILRt1/0Op98QL981DXPdiD2fliZZx3m3z7Keg86wOZZ/WvCw6
AF06rxFSFU1L6y7T19NdjrvsPdedh88N4PK/Osw7yfrzoPOsD7PMs85rXEWu37sZS6iaHV3sQhYY6flNr7USHcR5Xa81EW0JZXiz0e5AFGPjbPBX6o2+

Segmento MSH.
 Posición   Valor                           Tipo   Nombre                      Características
 MSH.1      MSH                               R    Field Separator             Valor fijo
 MSH.2      ^~\&                              R    Encoding Characters         Valor fijo
 MSH.3      DNLAB                             O    Sending Application         Sistema emisor
 MSH.4      DEDALUS                           O    Sending Facility            Sistema emisor




Definición mensaje MDM^T02 de envio de informes

                                                                                                               Documentacion Tecnica

                                                                                                                         Contenidos



 MSH.5      SISTEMAEXTERNO                   O     Receiving Application     Sistema receptor
 MSH.6      SISTEMAEXTERNO                   O     Receiving Facility        Sistema receptor
 MSH.7      20230404131845                   R     Date/Time Of Message      Fecha (YYYYMMDDHHMMSS)
 MSH.9      MDM^T02^MDM_T02                  R     Message Type              Valor fijo
 MSH.10     7288720230404131845              R     Message Control ID        Numero único que nunca se repite
 MSH.11     P                                R     Processing ID             Valor fijo
 MSH.12     2.5                              R     Version ID                Valor fijo

Segmento EVN.
 Posición   Valor                           Tipo   Nombre                    Características
 EVN.0      EVN                               R    Field Separator           Valor fijo
 EVN.1      T02                               R    Event Type                Valor fijo
 EVN.2      20230404141842                    O    Recorded Date/Time        Fecha del momento
 EVN.6      20230404141842                    O    Event Occurred            Fecha del momento

Segmento PID
 Posición    Valor                          Tipo   Nombre                    Características
 PID.0       PID                              R    Field Separator           Valor fijo
 PID.1                                        0    Set ID - PID              Valor fijo
 PID.3       1105582^^^CODEST^CODEST~         R    Patient Identifier List   Identificadores del paciente para dnlab:
             2345699^^^CF^CF~                                                Posición 1→ Código externo
             2345699^^^CS^CS~                                                Posiciones 4 y 5 → Flag identificatorio
             5800102851^^^LIS^LIS                                            CODEST – flag que identifica al paciente externo
                                                                             CS – flag que identifica la historia clínica
                                                                             CF – flag que identifica el número de documento
                                                                             LIS – flag que identifica el numero interno del lis
 PID.5       XXX^XXXXXX                       R    Patient Name              Posición 1 → Apellido
                                                                             Posición 2 → Nombre/s
 PID.6                                       O     Mother's Maiden Name      Apellido Materno o segundo apellido
 PID.7       19381021                        O     Date/Time of Birth        Fecha de nacimiento en formato (YYYYMMDD)




Definición mensaje MDM^T02 de envio de informes

                                                                                                                Documentacion Tecnica

                                                                                                                         Contenidos



 PID.8       F                               O     Administrative Sex          Sexo ( F – M – N)
 PID.11      GARIBALDI 355^^999BAI^^^^L      O     Patient Address             Posicion 1 → Dirección
                                                                               Posición 3→ Código Postal
                                                                               Posición 7 → Valor fijo


Segmento PV1
 Posición   Valor                           Tipo   Nombre                      Características
 PV1.0      PV1                               R    Field Separator             Valor fijo
 PV1.1      1                                 O    Set ID - PV1                Valor fijo
 PV1.2      U                                 O    Patient Class               Tipo Paciente
                                                                               E – Externo
                                                                               I – Internado
                                                                               O – Ambulatorio
                                                                               U - Emergencia
 PV1.3      INT-SSL^^^^^^^^Piso (SSL)        O     Assigned Patient            Identificadores de la ubicación del paciente.
                                                   Location                    Posición 1 → servicio donde se va a enviar el informe
                                                                               “Servicio Informe”
                                                                               Posición 8 → descripción del servicio
 PV1.19     12                               O     Visit Number                Numero de visita

Segmento TXA
 Posición    Valor                          Tipo   Nombre                      Características
 TXA.0       TXA                              R    Field Separator             Valor fijo
 TXA.1       1                                0    Set ID - TXA                Valor Fijo
 TXA.2       LD                               B    Document Type               Valor Fijo
 TXA.3       PDF                              R    Type of Referenced Data     Valor Fijo
 TXA.4       20230403174600                   B    Activity Date/Time          Fecha (YYYYMMDDHHMMSS)
 TXA.5                                        R    Primary Activity Provider
                                                   Code/Name




Definición mensaje MDM^T02 de envio de informes

                                                                                                        Documentacion Tecnica

                                                                                                                 Contenidos



 TXA.6       20230131191301                  O     Origination Date/Time    Fecha (YYYYMMDDHHMMSS)
 TXA.9                                       B     Originator Code/Name
 TXA.12      272393                          B     Unique Document          ID Documento DNLAB
                                                   Number
 TXA.14      4842                            O     Placer Order Number      Nro. De solicitud externa
 TXA.15      5800127893                      O     Filler Order Number      Nro. De solicitud DNLAB
 TXA.17      F                               O     Document Availability    P--> Parcial
                                                   Status                   F --> Final


Segmento OBX
 Posición   Valor                           Tipo   Nombre                   Características
 OBX.0      OBX                               R    Field Separator          Valor fijo
 OBX.1                                        R    Set ID - OBX
 OBX.2      ED                                O    Value Type               Valor fijo
 OBX.3      DOCUMENTO                              Observation Identifier   Valor Fijo
 OBX.5      ^multipart^ZPDF^Base64^                Observation Value        Informe en Base 64
            JVBERi0xLjIgCiXi48




Definición mensaje MDM^T02 de envio de informes

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
