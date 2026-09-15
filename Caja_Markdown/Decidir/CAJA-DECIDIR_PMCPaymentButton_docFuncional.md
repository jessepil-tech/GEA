Fuente original: PMCPaymentButton_docFuncional.pdf
Ruta original: Caja/PMCPaymentButton_docFuncional.pdf
Formato original: PDF

                                                               PMC Payment Button

                             PMC Payment Button
                            Documentación Funcinal

 Fecha            Versión      Autor     Descripción

 15/07/20         V 1.0        CP        Especificación API Payment Button + interconexión

 01/09/20         V 2.0        CP        Se agregan códigos de rechazo

 17/12/20         V3.0         CP        Se incluyen flujos del servicio

 15/04/2021       V3.1         ELL       Se corrige descripción del servicio

 03/04/2022       V3.2         ELL       Se corrige campo retur url

 15/03/2022       V3.3         ELL       Se corrige campo currency

 21/03/2022       V4.4         ELL       Se agrega servicio de Access Token y url por
                                         ambiente
 12/05/2022       V4.5         ELL       Se agregan campos para setear el tiempo de vida
                                         del token
 05/10/2022       V4.6         ELL       Se agrega pantallas del formulario de pago




Introducción:

  El servicio de botón de pago de PagoMisCuentas permite a las empresas
  recaudadoras generar un token con uno o más conceptos de pago, para luego
  realizar una interconexión al sitio de pagomiscuentas.com y permitir al usuario
  realizar el pago en línea de las facturas correspondientes.

Contenido:

   •   Flujos del servicio de botón de pago
   •   Cómo acceder a la API Payment buttton a través del Portal de API Manager
   •   Detalle del servicio Access Token
   •   Detalle de parámetros de la API

                                                                     PMC Payment Button

•   Detalle de códigos de respuesta
•   Interconexión a Sitio Seguro de PagoMisCuentas
•   URL por ambientes
•   Ejemplo navegación en formulario de pago


    1) Flujos del servicio:

    a) Generación de Token e interconexión




El usuario presiona el botón pagar, luego la empresa recaudadora genera la invocación de la API payment
button método /generate_token. En la respuesta del servicio obtendrá el token PKCS7 que luego será
utilizado para realizar la interconexión al sitio de PMC, desde el navegador del usuario. Toda esta
operatoria debe ser transparente para el usuario.


    b) Flujode pago y URL de retorno

                                                                            PMC Payment Button

El usuario podrá visualizar en el sitio las facturas a pagar, y tendrá que completar los datos del medio de pago
que le solicita el formulario de PagoMisCuentas. Una vez realizado el pago, podrá visualizar el comprobante,
descargarlo y retornar a la URL de la empresa de servicios si lo desea.

        2) Cómo acceder a la API Payment Button

El primer paso para la utilización del servicio es Registrarse en el Portal de APIs de Prisma Medios de Pago a
través de https://developers.prismamediosdepago.com




Una vez registrado y habiendo solicitado acceso a las Apis, mediante Soporte->Formulario de Soporte, se
debe crear un nuevo Proyecto y asociarle la API PMC Button:

PMC Payment Button

                                                                            PMC Payment Button




Si se requiere la utilización de alguna otra API, es conveniente agregar todas las necesarias al proyecto, ya
que una vez solicitado el pasaje a los ambientes de Homologación o Producción no se pueden agregar más
APIs al mismo.

También es necesario gestionar el Partner_ID, mediante Compañía->Tablero, ya que es necesario contar con
este identificador de cliente para quedar habilitado a solicitar pasaje de ambientes.




Una vez realizados estos pasos, se puede comenzar a utilizar la API, para lo cual se cuenta con la
documentación correspondiente en formato Swagger:

                                                                             PMC Payment Button




El servicio a utilizar es el POST /v1/generate_token.




    3) Detalle del servicio Access Token

Para cualquier API de PMC, previo a utilizar cualquier servicio dentro de la API, es necesario llamar al servicio
Access Token para generar el valor del atributo Authorization que debe estar presente en el header de cada
servicio.
Se trata de un servicio tipo GET, cuya URL es
https://api.prismamediosdepago.com/v1/oauth/accesstoken?grant_type=client_credentials

Se debe generar un OAUTH con las credenciales pública y privada del proyecto donde se tenga cargada la API,
según el ambiente (Sandbox, Homologación, Producción) con Base64 (https://www.base64encode.org/)
El OAUTH generado debe se utilizado en el atributo Authorization dentro del Header con el valor “Bearer
<oauth>”
También debe estar presente en el Header el atributo grant_type con el valor “client_credentials”

                                                                          PMC Payment Button

Luego, en el Body, utilizando el formato x-www-form-urlencoded informar los siguientes atributos
   • client_id, con el valor de la API Key pública obtenido de la opción Credenciales, dentro del proyecto
   • client_secret, con el valor de la API Key secreta obtenido de la opción Credenciales, dentro del
        proyecto

   4) Detalle de parámetros:
Request:
 Campo                  Tipo de       Mandatorio      Observaciones
                        Dato

 customer

   email                string (65)   'N'             Email del usuario al que le llegó la factura (solo
                                                      botón por mail).

  terminal              string (20)   'S'             Número de terminal. Alfanumérico sin caracteres
                                                      especiales.

  ip_address            string (15)   'S'             Que se encuentre dentro de los rangos de IP
                                                      existentes.

 customer_id            string (19)   'S'             Identificación del cliente de la empresa. Dato
                                                      numérico.

 document_type          string (3)    'S'             Tipo de documento del Operador que ejecutará la
                                                      transacción.
                                                      Valores posibles:
                                                      DNI= DNI
                                                      CI = Cédula de Identidad
                                                      PAS = Pasaporte
                                                      LC = Libreta Cívica
                                                      LE = Libreta de Enrolamiento

 document_number        string (12)   'S'             Número de documento del Operador que ejecutará
                                                      la transacción.

 invoices

 currency               string (3)    'S'             Moneda del importe de la transacción.
                                                      Valores posibles:
                                                          •   USD
                                                          •   ARS

 amount                 double        'S'             Importe de la transacción: 10 enteros y 2 decimales.
                        (10,2)

  invoice_id            string (20)   'S'             Número de factura del cliente de la empresa.

                                                                                PMC Payment Button

Campo                            Tipo de       Mandatorio   Observaciones
                                 Dato

    due_date                     date (24)     'S'          Fecha de vencimiento. Formato ISO

additional_data                                ‘S’

return_url                       string        'N'          URL a la que devolver al usuario una vez que
(opcional)                       (800)                      completa el pago:

validation_title                 string        'N'          Título del texto a validar
                                 (800)

validation_value                 double        'N'          Valor del texto a validar
                                 (30)

brand_url                        String(800)   'N'          URL pública donde se encuentra hosteada la imagen
                                                            del logo de la empresa. puede venir vacío.

date_from                        date (24)     'N'          Fecha de inicio vigencia del token. Formato ISO

date_to                          date (24)     'N'          Fecha de fin vigencia del token. Formato ISO

connection_data                                ‘S’

channel                          string (1)    'S'          Canal – alfanumérico 1 dígito
                                                            Valor posible = K

origin                           string (1)    'S'          Origen de la transacción – numérico 1 dígito
                                                            Valor posible = 2

company_fiid                     String(4)     'S'          FIID de la empresa, otorgado por Prisma
                                                            String alfanumérico de 4 dígitos



Ejemplo request:

{

    "customer": {

         "email": "string",

         "terminal": "string",

         "ip_address": "string",

         "customer_id": "string",

         "document_type": "string",

         "document_number": "string"

    },

                                                                                  PMC Payment Button

     "invoices": [

          {

              "currency": "string",

              "amount": 0,

              "invoice_id": "string",

              "due_date": "2022-05-12T14:08:51.856Z"

          }

     ],

     "additional_data": {

          "return_url": "string",

          "brand_url": "string",

          "validation_title": "string",

          "validation_value": 0,

          "date_from": "2022-05-12T14:08:51.856Z",

          "date_to": "2022-05-12T14:08:51.856Z"

     },

     "connection_data": {

          "channel": "string",

          "origin": "string",

          "company_fiid": "string"

     }

 }



Response:
 Campo                 Tipo de Dato       Descripción

 code                  string             Devuelve el código de la respuesta

 message               string             Descripción del código de respuesta

 Token                 string             Token PKC27 generado para realizar el pago.

 Version               string             Indica la versión del Token generado.

                                                                         PMC Payment Button

En el caso de HTTP 200 significa que el token se generó correctamente, y el mismo está dentro del campo
“token” de la respuesta, el campo “versión” es un campo que indica la versión del token generado (necesario
para el siguiente paso)

Ejemplo de respuesta HTTP 200:




   5) Detalle de códigos de respuesta:
Códigos de Respuesta:
 Código       Descripción                  validación                                      Status

 00           Aprobado                                                                     "OK"

 IP01         customer_id is not valid     tipo de dato o longitud incorrecto              Error. Bad
                                                                                           Request

 IP13         customer_id is required      dato vacío o no presente                        Error. Bad
                                                                                           Request

 IP02         terminal is not valid        tipo de dato o longitud incorrecto              Error. Bad
                                                                                           Request

                                                                  PMC Payment Button

Código   Descripción               validación                                    Status

IP14     terminal is required      dato vacío o no presente                      Error. Bad
                                                                                 Request

IP03     ip_adress is not valid    tipo de dato o longitud incorrecto            Error. Bad
                                                                                 Request

IP15     ip_adress is required     dato vacío o no presente                      Error. Bad
                                                                                 Request

IP52     email is not valid        tipo de dato o longitud incorrecto (si está   Error. Bad
                                   presente)                                     Request

IP53     document_type is not      tipo de dato o longitud incorrecto            Error. Bad
         valid                                                                   Request

IP16     document_type is          dato vacío o no presente                      Error. Bad
         required                                                                Request

IP54     document_number is not    tipo de dato o longitud incorrecto            Error. Bad
         valid                                                                   Request

IP17     document_number is        dato vacío o no presente                      Error. Bad
         required                                                                Request

IP05     due_date is not valid     tipo de dato o longitud incorrecto            Error. Bad
                                                                                 Request

IP18     due_date is required      dato vacío o no presente                      Error. Bad
                                                                                 Request

IP10     invoice_id is not valid   tipo de dato o longitud incorrecto            Error. Bad
                                                                                 Request

IP19     invoice_id is required    dato vacío o no presente                      Error. Bad
                                                                                 Request

IP11     Currency is not valid     tipo de dato o longitud incorrecto            Error. Bad
                                                                                 Request

IP58     currency is required      dato vacío o no presente                      Error. Bad
                                                                                 Request

IP12     amount is not valid       tipo de dato o longitud incorrecto            Error. Bad
                                                                                 Request

IP59     amount is required        dato vacío o no presente                      Error. Bad
                                                                                 Request

IP20     return_url is not valid   tipo de dato o longitud incorrecto (cuando    Error. Bad
                                   está presente)                                Request

                                                                           PMC Payment Button

  Código     Descripción                     validación                                Status

  IP55       validation_title is not valid   tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request

  IP56       validation_value is not         tipo de dato o longitud incorrecto        Error. Bad
             valid                                                                     Request

  IP57       brand_url is not valid          tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request

  IP30       channel is not valid            tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request

  IP60       channel is required             dato vacío o no presente                  Error. Bad
                                                                                       Request

  IP31       origin is not valid             tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request

  IP61       origin is required              dato vacío o no presente                  Error. Bad
                                                                                       Request

  IP32       company_fiid is not valid       tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request

  IP50       company_fiid is required        Dato vacío                                Error. Bad
                                                                                       Request

  IP72       Time out                        time out                                  Error

  IP99       Error genérico                  error genérico                            Error

  IP62       At least one invoice is         estructura invoices no presente           Error. Bad
             required                                                                  request

  IP65       date from is not valid          tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request

  IP66       date to is not valid            tipo de dato o longitud incorrecto        Error. Bad
                                                                                       Request


    6) Interconexión:

Una vez obtenido el Token PKCS7, se debe crear un HTML con un formulario del tipo POST que contenga el
token / versión y cuya acción sea https://paysrv3.pagomiscuentas.com/pmctas/direct_payment.do, se debe
hacer submit del formulario en el momento que termina de cargar.

Ejemplo de HTML

<html>

                                                                                           PMC Payment Button

<head>
<title>&nbsp;</title>
<meta http-equiv="PRAGMA" content="NO-CACHE"/>
<meta http-equiv="Expires" content="0"/>
</head>
<body onload="document.form.submit()">
<form name="form" method="POST" action="https://paysrv3.pagomiscuentas.com/pmctas/direct_payment.do" >
                      <input type="hidden" name="version" value="1">
                      <input type="hidden" name="token" value="-----BEGIN PKCS7-----
MIAGCSqGSIb3DQEHAqCAMIACAQExCzAJBgUrDgMCGgUAMIAGCSqGSIb3DQEHAaCA
JIAEggHFeyJjdXN0b21lciI6eyJlbWFpbCI6InByb2phc0BsaXZlLmNvbS5hciIs
InRlcm1pbmFsIjoiUEMtUk9KQVMiLCJpcF9hZGRyZXNzIjoiMTAuMTEuMTIuMTMi
LCJjdXN0b21lcl9pZCI6IjEyMzQ1Njc4IiwiZG9jdW1lbnRfdHlwZSI6IjEiLCJk
b2N1bWVudF9udW1iZXIiOiIzMzY1NTMwMyJ9LCJpbnZvaWNlcyI6W3siY3VycmVu
Y3kiOiIwMzIiLCJhbW91bnQiOjEwLjAsImludm9pY2VfaWQiOiI4NzY1NDMyMSIs
ImR1ZV9kYXRlIjoxNTUwNjkwODEwMTEwfV0sImFkZGl0aW9uYWxfZGF0YSI6eyJy
ZXR1cm5fdXJsIjoiaHR0cHM6Ly93d3cubWV0cm9nYXMuY29tLmFyIiwidmFsaWRh
dGlvbl90aXRsZSI6Ik5ybyBNZWRpZG9yIiwidmFsaWRhdGlvbl92YWx1ZSI6MzM0
NDM0M30sImNvbm5lY3Rpb25fZGF0YSI6eyJjaGFubmVsIjoiSyIsIm9yaWdpbiI6
IjEiLCJjb21wYW55X2ZpaWQiOiJNVEdBIn19AAAAAAAAoIIFKTCCBSUwggQNoAMC
AQICAgCzMA0GCSqGSIb3DQEBCwUAMIGmMQswCQYDVQQGEwJBUjEVMBMGA1UECBMM
QnVlbm9zIEFpcmVzMRgwFgYDVQQHEw9DYXBpdGFsIEZlZGVyYWwxIzAhBgNVBAoT
GlByaXNtYSBNZWRpb3MgZGUgUGFnbyBTLkEuMR4wHAYDVQQLExVTZWd1cmlkYWQg
SW5mb3JtYXRpY2ExITAfBgNVBAMTGFByaXNtYSBTdWJDQSBpbnRlcm5hbCBHNTAe
Fw0xNjAzMjMxMjUzMDJaFw0yMTAzMjIxMjUzMDJaMIGlMQswCQYDVQQGEwJBUjEV
MBMGA1UECBMMQnVlbm9zIEFpcmVzMRgwFgYDVQQHEw9DYXBpdGFsIEZlZGVyYWwx
IzAhBgNVBAoTGlByaXNtYSBNZWRpb3MgZGUgUGFnbyBTLkEuMSkwJwYDVQQLEyBT
ZWd1cmlkYWQgSW5mb3JtYXRpY2EgLSBDb2RlU2lnbjEVMBMGA1UEAxMMQmFuZWxj
BKGoapL4QfTAG/A5o/+B4ey1BAOMfHy0vB+6B0zrncKrR3JdYHVT2KvdXf7raF0h
t65CadOZh1ny2+3aVt6WXV7Gk5do7sMGjQMthDYqQCvQiQOU1X51GQ9N92P9PW9S
X7S2hT9xAAAAAAAA
-----END PKCS7-----">
</form>
</body>
</html>




    7) URLs por ambientes
 Sandbox
 URL para API GEE
 Para solicitar el Access Token
 https://api-
 sandbox.prismamediosdepago.com/v1/oauth/accesstoken?grant_type=client_credentials

 Para generar el Token PKCS7
 https://api-sandbox.prismamediosdepago.com/v1/payment_button/generate_token

 Homologación
 URL para API GEE
 Para solicitar el Access Token
 https://api-
 homo.prismamediosdepago.com/v1/oauth/accesstoken?grant_type=client_credentials

 Para generar el Token PKCS7
 https://api-homo.prismamediosdepago.com/v1/payment_button/generate_token
 URL para el formulario de pago
 https://paysrv3-homo.pagomiscuentas.com/pmctas/direct_payment.do

 Producción
 URL para API GEE

                                                                      PMC Payment Button

Para solicitar el Access Token
https://api-prismamediosdepago.com/v1/oauth/accesstoken?grant_type=client_credentials

Para generar el Token PKCS7
https://api.prismamediosdepago.com/v1/payment_button/generate_token
URL para el formulario de pago
https://paysrv3.pagomiscuentas.com/pmctas/direct_payment.do



  8) Ejemplo navegación en formulario de pago

PMC Payment Button

[ELEMENTO VISUAL NO CONVERTIDO: imagen]
