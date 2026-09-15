Fuente original: FLENI  - Mensajes HL7 de resultados lab general.pdf
Ruta original: Laboratorio/DNLAB/FLENI  - Mensajes HL7 de resultados lab general.pdf
Formato original: PDF

*-OML^O21 (Mensaje de petición de análisis desde el HIS - Internados)
MSH|^~\&|FLEI|FLENI|DNLAB|DIANOEMA|20160407115428||OML^O21^OML_O21|hce1460040867320|P|2.5
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
OBX|1|NM| 11537-0^MOT1^LN|| diagnostico por código||||||F|||
OBX|2|NM|11539-0^MOTDESC^LN|| diagnostico escrito||||||F|||


MSH|^~\&|FLENI|FLENI|DNLAB|DIANOEMA|20160407115428||OML^O21^OML_O21|hce1460040867320|P|2.5
MSH.2 y MSH.3 → order entry y facility name del sistema que envia, ponemos FLENI
MSH.4 y MSH.5 → order entry y facility name del sistema que recibe , ponemos DNLAB y DIANOEMA
MSH.6 → Fecha del mensaje
MSH.9 → numero de mensaje que no se debe repetir en ningún caso

PID|1||50546^^^CODEST^CODEST~64849^^^CS^CS~4064825^^^CF^CF||XXX^XXX XXX||19301003|M|||VIRREY DEL PINO N° 1736 PISO 16 DTO
A^^^||4783-5856 1536927843^PRN~a@hotmail.com^NET||S|S
PID.3 → CODEST es el número de paciente externo, CS es el número de histórica clínica y CF es el numero de documento
PID.5 → PID.5-1 apellido y PID.5-2 nombres
PID.7→ fecha de nacimiento en formato YYYYMMDD
PID.8 → Genero o sexo

PID.11 → dirección
PID.13 → teléfono de contacto

PV1|1|H|F5103^^4006|||||||||||||||A|167470
PV1.2 → tipo de orden (internado u hospitalizado)
PV1.3 → PV1.3-1 es el servicio que solicita la orden y PV1.3-4 es la cama en que se encuentra el paciente
PV1.19 → es el número de episodio clínico

ORC|NW|hce14600408673201||hce1460040867320|IP||||20160408060000|||2000000000515A2F^Cabañuz^Florencia|||20160407115428||
||N2_LABOR^001
ORC.1 → señal de nuevo NW
ORC.2 → numero de solicitud externa concatenada con la secuencia que determina el analisis
ORC.4 → numero de solicitud externa
ORC.9 → fecha de la solicitud
ORC.12 → medico que solicita la orden (código^apellido^nombre)

TQ1|01||||||||N
TQ1.9 → nivel de urgencia de la orden

OBR|01|hce14600408673201||GLU^|R|||||||||||2000000000515A2F^Cabañuz^Florencia
OBR.2 → numero de solicitud externa acompañado de la secuencia
OBR.4 → OBR.4-1 codigo del análisis pedido
OBR.16 → medico que solicita la orden (código^apellido^nombre)


OBX|1|NM| 11537-0^MOT1^LN|| diagnostico por código||||||F|||
OBX|2|NM|11539-0^MOTDESC^LN|| diagnostico escrito||||||F|||
OBX.3 → valor fijo para indicar que me están enviando
OBX.5 → valor variable

*-ORL^O22 (Mensaje de respuesta a los análisis enviados)
MSH|^~\&|DNLAB|DIANOEMA|FLENI|FLENI|20160615115343||ORL^O22^ORL_O22|HL7Gtw0155548DBDED00|P|2.5||||||8859/1
MSA|AA|PIC24efdc11f01e8432
PID|1||450081^^^CODEST&ID Paciente^CODEST~450081^^^CS&ID
Expediente^CS~465312^^^CF&DNI^CF~||CAS^JORGE||19480602|M|||||456464165^PRN
ORC|OK|7200101801|000000047820160614151910^DN|72001018|||||20160615121200
TQ1|||||||||R
OBR||7200101801|000000047820160614151910^DN|UPSP||||||||||||200000000000AC34^PRUEBA 1^prueba UNO
ORC|OK|7200101803|000000047820160614151930^DN|72001018|||||20160615121200
TQ1|||||||||R
OBR||7200101803|000000047820160614151930^DN|MCOPSP||||||||||||200000000000AC34^PRUEBA 1^prueba UNO
ORC|OK|7200101802|000000047820160614151920^DN|72001018|||||20160615121200
TQ1|||||||||R
OBR||7200101802|000000047820160614151920^DN|MDOPSP||||||||||||200000000000AC34^PRUEBA 1^prueba UNO

ORC|OK|7200101801|000000047820160614151910^DN|72001018|||||20160615121200
ORC.1→ si se ingreso correctamente
ORC.2→ solicitud y secuencia del análisis

OBR||7200101801|000000047820160614151910^DN|UPSP||||||||||||200000000000AC34^PRUEBA 1^prueba UNO
ORC.2→ solicitud y secuecia
ORC.4→codigo del analisis




*-OUL^R22 (Mensaje de resultados de los análisis)

MSH|^~\&|DNLAB|DIANOEMA|FLENI|FLENI|20160531173606.1810+0100||OUL^R22^OUL_R22|HL7Gtw01550775272500|P|2.5||||||8859/1
PID|1||0000000024^^^LIS^LIS~12493604^^^CF^CF||PRUEBA^RICARDO||19561208|M|||||||S|S
PV1|1|A|F5400^^^^^^^^^^Guardia||||||||||||||||00000043
SPM|1|000000020302||SUE^Suero|||||||||||||20160531113400
OBR|1||10000000203201605311134^DN^20160000000203|GOT^Aspartato Aminotransferasa
(GOT)^DN^GOT@1^^DN|||201605311134|||||||||||||||||LAB-1|F||^^^201605311134
ORC|SC||10000000203201605311134^DN^20160000000203||CM||201605311134||20160531123557||||||||||||Guardia^FI^F5400
TQ1|1||||||201605311134||U
OBX|1|NM|GOT^Aspartato Aminotransferasa (GOT)^DN^GOT@1^^DN||23|UI/l|15 - 40|N|||F|||20160531123300|^QUIMICA^2^10-0|RSANZ
OBR|2||10000000203201605311134^DN^20160000000203|GPT^Alanina Aminotransferasa
(GPT)^DN^GPT@1^^DN|||201605311134|||||||||||||||||LAB-1|F||^^^201605311134
ORC|SC||10000000203201605311134^DN^20160000000203||CM||201605311134||20160531123557||||||||||||Guardia^FI^F5400
TQ1|1||||||201605311134||U
OBX|1|NM|GPT^Alanina Aminotransferasa (GPT)^DN^GPT@1^^DN||31|UI/l|10 - 40|N|||F|||20160531123300|^QUIMICA^2^11-0|RSANZ
OBR|3||10000000203201605311134^DN^20160000000203|PTT^Prote\X00ED\nas
Totales^DN^PTT@1^^DN|||201605311134|||||||||||||||||LAB-1|F||^^^201605311134
ORC|SC||10000000203201605311134^DN^20160000000203||CM||201605311134||20160531123557||||||||||||Guardia^FI^F5400
TQ1|1||||||201605311134||U
OBX|1|NM|PTT^Prote\X00ED\nas Totales^DN^PTT@1^^DN||7.9|g/dl|6.4 - 8.3|N|||F|||20160531123300|^QUIMICA^2^12-0|RSANZ


PID|1||0000000024^^^LIS^LIS~12493604^^^CF^CF||PRUEBA^RICARDO||19561208|M|||||||S|S
PID.3 → LIS es el código interno de paciente de dnlab, este va a acompañar a los otros códigos del paciente.

SPM|1|000000020302||SUE^Suero|||||||||||||20160531113400
SPM.2 → es el código de muestra o el identificador de la etiqueta

OBR|2||10000000203201605311134^DN^20160000000203|GPT^Alanina Aminotransferasa
(GPT)^DN^GPT@1^^DN|||201605311134|||||||||||||||||LAB-1|F||^^^201605311134
OBR.4^1 → código del análisis

OBX|1|NM|GPT^Alanina Aminotransferasa (GPT)^DN^GPT@1^^DN||31|UI/l|10 - 40|N|||F|||20160531123300|^QUIMICA^2^11-0|RSANZ
OBX.5 → resultado del estudio
OBX.6 → unidad de medida
OBX.7 →valores de referencia
OBX.14 → fecha de validación
OBX.15 → usuario de la validación