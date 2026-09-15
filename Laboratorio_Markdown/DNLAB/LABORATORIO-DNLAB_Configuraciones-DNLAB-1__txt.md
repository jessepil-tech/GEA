Fuente original: Configuraciones DNLAB (1).txt
Ruta original: Laboratorio/DNLAB/Configuraciones DNLAB (1).txt
Formato original: TXT

--Configuraciones

 - Laboratorio => Configuración => Interface Laboratorio => Tipo Interface Laboratorio => Agregar => Tipo Interface Laboratorio: DNLAB
                                                                                                     Agrupación Archivo: CÓD. BARRA MUESTRA
                                                                                                     check Implementación Nativa
                                                                                                     Tipo Implementacion Nativa: DNLAB
 
 AGREGAR CONFIGURACION DE TIPO ETIQUETA LAB
 - Laboratorio => Configuración => Muestra Laboratorio => Tipo Etiqueta Laboratorio => Agregar => Tipo Etiqueta: ZEBRA DNLAB
                                                                                                  Tipo Impresora Etiquetas: ZEBRA
                                                                                                  Tipo Programación: COMANDO
                                                                                                  Cantidad Etiquetas Hoja: 1
                                                                                                  Distancia Entre Etiquetas: 1
 
 - Laboratorio => Configuración => Muestra Laboratorio => Tipo Etiqueta Laboratorio => buscar ZEBRA DNLAB => Detalle Tipo Etiqueta => Agregar (detalle) => ver tabla anexa. o ver ejemplo en otro cliente
 
 
 - Administración General => Configuración Operativa => Centro Atención => Centro Atención => elegir centro => Centro Atención: Cod. Centro Interface Lab. ingresar còdigo proporcionado por Centralab, para pruebas Medicus es PROLI
 - Administración General => Configuración Operativa => Centro Atención => Servicio Centro => buscar Laboratorio => Laboratorio: Tipo Etiqueta Laboratorio: SATO CX400 o ZEBRA???
                                                                                                                                 check Requiere Interfaz Derivaciones Laboratorio 
                                                                                                                                 Interfaz Derivaciones Laboratorio seleccionar DNLAB 
 - Laboratorio => Configuración => Muestra Laboratorio => Recipiente Muestra => Agregar => Código: 222
                                                                                           Recipiente Muestra: DNLAB
                                                                                           Color Tapa: ver
                                                                                           Texto color Tapa: TAPA
                                                                                           Color Borde Tapa: ver
                                                                                           Texto color borde Tapa: BORDE
                                                                                           Orden Extracción: 1
                                                                                           Volumen Muestra: 1
   - Laboratorio => Configuración => Muestra Laboratorio => Especímen => Agregar => Especímen: DNLAB
                                                                                    Abreviación: DNLAB
                                                                                    Nro. Orden: 1                                                                                       

- SE DEBEN CONFIGURAR LAS PRESTACIONES ASOCIADAS A UN COD_ANALISIS_LAB QUE SE TOMA EN EL MENSAJE DE PEDIDO.
- VER: SE DEBEN CONFIGURAR PARIDADES TIPO INTERFACE LAB SI LLEGA A SER NECESARIO (hasta el momento, en etapa de desarrollo/test, no es necesario)

http://IP_SERVIDOR_APLICACION:8080/WS-HOSPITAL-TEST/rest/mensajesDNLab

--Endpoint
insert into param_laboratorio (fecha_vigencia, interface_lab, dir_interface_lab_ing_datatech, dir_interface_lab_egr_datatech, servidor_lab_ext, base_datos_lab_ext, usuario_lab_ext, password_lab_ext, fecha_last_update, actualizado_por, url)
values (to_date('01-05-2023 09:00:52', 'dd-mm-yyyy hh24:mi:ss'), 'DNLAB', null, null, null, null, null, null, to_date('10-03-2023 09:00:52', 'dd-mm-yyyy hh24:mi:ss'), 'DEMO', 'http://10.200.12.17:4222/soap/IDNLabResultadoWS');