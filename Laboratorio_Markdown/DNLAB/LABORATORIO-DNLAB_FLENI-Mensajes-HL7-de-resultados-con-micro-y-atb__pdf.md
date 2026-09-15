Fuente original: FLENI  - Mensajes HL7 de resultados con micro y atb.pdf
Ruta original: Laboratorio/DNLAB/FLENI  - Mensajes HL7 de resultados con micro y atb.pdf
Formato original: PDF

           DOCUMENTACIÓN TECNICA
MENSAJES HL7 DE RESULTADOS DE BACTERIO CON MICRO Y ATB

1. Datos del documento:


Documentación
Fecha                   Autor                                 Objeto
15/11/2019              Rodriguez Marcelo                     Primera emisión documento


2. Mensaje de resultados de Bacteriología:

Este mensaje se envía desde el HIS para que se cree la orden en el LIS.

MSH|^~\&|DNLAB|NOEMALIFE|SLL|SLL|20170630050509.8490+0100||OUL^R22^OUL_R22|HL7Gtw015CF6F599D900|P|2.5||||||8859/1
PID|1||0000016930^^^LIS^LIS~5690129^^^CODEST^CODEST~5690129^^^CS^CS~93946691^^^CF^CF||xxxxx^xxxxx||111111111|M|||||||S|S
PV1|1|A|SLL^^-^^^^^^^^LAS LOMAS||||||||||||||||00028535
SPM|1|170001122801||ORIN^Orina Chorro Medio|||||||||||||20170629074300
OBR|1|52583901|525839^DN^20171700011228|UROC^^DN^UROC@1^^DN|||201706290743|||||||||MACCHI,ALEJANDRA MARCELA||||||||MICRO|C||^^^201706290743
ORC|SC|52583901|525839^DN^20171700011228|525839^DN|CM||201706290743||20170630000456|||MACCHI,ALEJANDRA MARCELA|||||||||LAS LOMAS^FI^SLL
TQ1|1||||||201706290743||R
OBX|1|TX|UROC^UROC.COMMENT1^DN|1|7 a 10 leucocitos por campo||||-1||C||||17^MICROBIOLOGIA^17^001000000
OBX|2|CE|URO^Urocultivo^DN^URO@1^^DN||DESA^Desarrollo de^Entre 100 y 1.000 UFC/ml|||N|1||C||1|20170629144447|17^MICROBIOLOGIA^17^0500030001|EHAAG
OBX|3|CE|SEDU^Sedimento Urinario^DN^SEDU@1^^DN||SOB^Se observan|||N|1||C|||20170629144319|17^MICROBIOLOGIA^17^0500030002|EHAAG
OBX|4|CE|SEDU1^^DN^SEDU1@1^^DN||CEESC^Escasas celulas epiteliales|||N|1||C|||20170629144319|17^MICROBIOLOGIA^17^0500030003|EHAAG
OBX|5|CE|SEDU2^^DN^SEDU2@1^^DN||L7-10^7 a 10 leucocitos por campo|||N|1||C|||20170629144319|17^MICROBIOLOGIA^17^0500030004|EHAAG
OBR|2|52583901|525839^DN^20171700011228|LIS_MIC^Microrganismo Identificado^DN|||201706290743|||||||||MACCHI,ALEJANDRA
MARCELA||||||||MICRO|C|URO|^^^201706290743||^UROC
ORC|SC|52583901|525839^DN^20171700011228|525839^DN|CM||201706290743||20170630000456|||MACCHI,ALEJANDRA MARCELA|||||||||LAS LOMAS^FI^SLL
OBX|1|CE|11475-1^Micro organism identified^LN|1|ECO^Escherichia coli^NOTA ATB|||N|||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBR|3|52583901|525839^DN^20171700011228|LIS_ATB^Antibiotico Probado^DN^AIUA|||201706290743|||||||||MACCHI,ALEJANDRA
MARCELA||||||||MICRO|C|URO|^^^201706290743||^UROC
ORC|SC|52583901|525839^DN^20171700011228|525839^DN|CM||201706290743||20170630000456|||MACCHI,ALEJANDRA MARCELA|||||||||LAS LOMAS^FI^SLL
OBX|1|SN|AMP^Ampicilina^DN^AIUA|1|||()|R|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG




Mensajes HL7 de muestra

OBX|2|SN|SXT^Trimetoprima/Sulfametoxazol ^DN^AIUA|1|||()|S|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBX|3|SN|CXM^Cefuroxima^DN^AIUA|1|||()|S|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBX|4|SN|SAM^Ampicilina/Sulbactam^DN^AIUA|1|||()|R|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBX|5|SN|CIP^Ciprofloxacina^DN^AIUA|1|^Sensibilidad disminuida a Quinolonas. Probable \X00E9\xito de tratamiento en infecci\X00F3\n urinaria baja no complicada de la
comunidad.||()|S|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBX|6|SN|CZO^Cefazolina^DN^AIUA|1|^En infecciones urinarias no complicadas sensible a todas las cefalosporinas
orales||()|S|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBX|7|SN|NAL^Acido Nalidixico^DN^AIUA|1|||()|R|0||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG
OBX|8|SN|NIT^Nitrofurantoina^DN^AIUA|1|||()|S|1||C|||20170629144447|17^MICROBIOLOGIA^17^001000001|EHAAG

3. Descripción de principales posiciones:

OBR^4.2 → descripción del análisis multiple
OBX^3.2 → descripción del análisis simple(componente del multiple).
      Si se encuentra en este lugar la palabra COMMENTX es un comentario al análisis (pongo X porque pueden ser
varios) y va asociado al análisis y no a los componentes.
OBX^5 → resultado.
OBX^6 → unidades de medida del resultado.
OBX^7 → valores de referencia.
OBX^15 → es la posición del análisis en el informe, por si necesitas que queden iguales.


MICROORGANISMO (OBR^4.1 te indica que es un microorganismo, te das cuenta por la sigla LIS_MIC)
OBR^26 → Analisis al que lo asociaron.
OBX^5 → Nombre del Microorganismo




Mensajes HL7 de muestra

ANTIBIOTICOS (OBR^4.1 te indica que es la lista de antibioticos, te das cuenta por la sigla LIS_ATB)
OBR^3.2 → descripción del antibiótico
OBX^5.2 → nota al antibiotico
OBX^8 → S- sensible/R – resistente / I - intermedio
OBX^9 → 1- se imprime en el informe / 0 – no se imprime en el informe.

4. Distribución u ordenamiento del mensaje en el informe.




Mensajes HL7 de muestra

Mensajes HL7 de muestra

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
