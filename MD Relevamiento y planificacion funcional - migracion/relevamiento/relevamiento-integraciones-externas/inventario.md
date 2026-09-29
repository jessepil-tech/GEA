---
title: Inventario — detalle de integraciones externas
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.relevamiento-integraciones-externas.inventario
---

# Detalle por integración

Resumen, correcciones de recorte y hallazgos: [`README.md`](README.md).
Rutas relativas a `Hospital-Legacy/`. **Ninguna credencial se transcribe acá**: donde el
código las tiene, se indica dónde viven.

## AFIP — factura electrónica

| Ítem | Valor |
|------|-------|
| Contraparte real | Facade Axis2 del proveedor (`facade.thinksoft.com.ar`), que resuelve WSFEv1 |
| Transporte | SOAP 1.2 sobre Axis2; URL leída de `ParamFactElectronicaDf.getUrl()` |
| Núcleo | `AFIP/src/.../implementations/ImpBusValidarComprobante.java` (~1.200 líneas) |
| Disparo | `ValidarComprobantesJob` + ~15 beans de facturación, caja y convenios; pantalla `pages/caja/comprobantesPendientesValidar.xhtml` |
| Operaciones | `SolicitarUltimoAutorizado` · `ConsultarCAEComprobante` · `ConstatarComprobante` · `ConsultarPtoVtaAsignados` · `ConsultarSiEsMiPyme` · `GetCotizacionMoneda` |
| Criticidad | Bloqueante: sin CAE no se emite factura válida |

Dos cosas para el diseño destino: no hay WSAA ni certificados en el proyecto (la
autenticación fiscal la hace el facade), y cada request incluye la contraseña de la base del
HIS como parámetro. Ver hallazgos 1 y 2 del README.

## ANMAT — trazabilidad de medicamentos y productos médicos

| Ítem | Valor |
|------|-------|
| Contraparte | Sistema Nacional de Trazabilidad, operado sobre infraestructura de PAMI (`trazabilidad.pami.org.ar:9050/trazamed.WebService`; entrenamiento en `servicios.pami.org.ar`) |
| Transporte | Dos servicios distintos: medicamentos con CXF + WS-Security (`WSTrazaMedClient`, ~695 líneas) y productos médicos con stubs Axis 1 (`WSTrazaProdClient`, ~437) |
| Datos | Eventos de ítems serializados (GTIN + lote + serie + GLN origen/destino); catálogos por GTIN/GLN; consulta de médicos por CUIT |
| Disparo | `InterfaceTrazabilidadJob`, tres jobs de catálogo, y sincrónico desde farmacia vía `ProcessAnmatServlet` (~973 líneas) — `pages/farmacia/consultaItemTrazable.xhtml`, `ajusteItemDepositoTrazable.xhtml` |
| Credenciales | Tabla de parámetros (`ParamTrazabilidad`), una por servicio |
| Criticidad | Alta pero diferible: obligación regulatoria que tolera informe posterior |

## VALIDADORES — diecisiete clientes de obras sociales

Despachador: `VALIDADORES/src/ar/com/thinksoft/ws/ValidadorWS.java` (~1.879 líneas), con un
`switch` de 29 `case` sobre `idValidadorOnline` que instancia 17 clientes.

| Cliente | Endpoint por defecto | Transporte |
|---------|----------------------|------------|
| Conexia | `comeipreprod.conexia.com.ar:4001/services` | SOAP Axis2 |
| Conexia UP | host fijo por IP | SOAP Axis2 |
| Traditum | `canalws.traditum.com/WebService_IA.asmx` | SOAP .NET, con 3DES propio |
| Sancor | `e.sancorsalud.com.ar/apawe_ssa_v4.aspx` | SOAP .NET |
| OSMECON | `techmedica-online.com.ar:8443/AutorizaOSMECON` | SOAP Axis2 |
| ITC / Sitel | `ws.itcsoluciones.com:48080/SitelServletWebService` | SOAP Axis2 |
| ITC REST | host interno | REST/JSON |
| Activia | `activiac.homeip.net/WSActiviaC.asmx` | SOAP .NET |
| APROSS (Córdoba) | `servicios2.test.apross.gov.ar` | REST |
| HMS (v1 y v10) | host interno | SOAP Axis2 |
| Facade propio (GEA/SEMEGER/CEMIC/DASPU) | `prestacionesgea.com.ar:8080/axis2/services/AUTORIZADOR` | SOAP Axis2 |
| UTA | de base | POST form-urlencoded |
| Swiss Medical · Medicus · OSPJN | de base | REST/JSON |

Contrato común: `elegibilidad` · `autorizarAmb` · `autorizarAdmisionInt` ·
`anularAutorizacion` · `registrarRealizaciones` · `grupoFamiliar` · `datosAfiliado` ·
`informarRecetaElectronica`.

Disparo **exclusivamente sincrónico desde pantalla**: ningún job lo invoca. Consumidores:
`AGI` (recepción e identificación de paciente en tótem), `HOS-APP`, `RECETAS` y `HOSPITAL_2`.
Credenciales por validador en `ValidadorOnlineDf`.

Nota de alcance vigente: los cortes de Turnos reusan el puerto de elegibilidad con seed y
dejan el WS real declarado en [`pendientes-solo-oracle.md`](../../estado/pendientes-solo-oracle.md)
(P-ORA-010). Este inventario dimensiona ese diferido: no es «un WS», son diecisiete.

## BIONEXO — marketplace de compras

| Ítem | Valor |
|------|-------|
| Contraparte | `webservice.bionexo.com` (namespace `bionexo.com.br`) |
| Transporte | SOAP Axis2 con dos operaciones genéricas (`post` / `request`); el payload real es XML anidado mapeado con JAXB en tres esquemas (pedidos, respuestas, catálogo) |
| Núcleo | `BIONEXO/src/.../ImpBusBionexo.java` (~1.302 líneas) |
| Disparo | `EnvioBionexoJob` · `RecepcionBionexoJob` + `pages/compras/consultaErroresBionexo.xhtml`, `categoriaEmpresaBionexo.xhtml` |
| Criticidad | Baja: compras tiene circuito manual y el portal `PROVEEDORES` cubre el mismo caso por otra vía |

En `RecepcionBionexoJob` el procesamiento de proveedores está comentado: uso parcial.

## ALFABETA — vademécum

| Ítem | Valor |
|------|-------|
| Contraparte | `abws.alfabeta.net/alfabeta-webservice/abWsDescargas` |
| Transporte | SOAP que devuelve un ZIP; los archivos planos se parsean en **IBM850** hacia tablas temporales (`MANUAL.DAT`, `MANUAL_NUEVO.DAT`, `MANUAL_VIEJO.DAT`, laboratorios, monodrogas, presentaciones) |
| Camino automático | **Incompleto**: descarga y no procesa (`//TODO procesar el archivo`, `ImpBusAlfabeta.java:376`) |
| Camino real | Carga manual en `pages/farmacia/alfaBetaUploadPage.xhtml` (bean `BBCargaArchivoAlfaBeta`), que sí descomprime y procesa |
| Credenciales | **Embebidas en el fuente** (`ImpBusAlfabeta.java:357`) — rotar y externalizar |

## Los dos portales (no son integraciones)

| Portal | Usuarios | Contenido | Clases |
|--------|----------|-----------|-------:|
| `RECETAS` | Médicos prescriptores externos | Receta oncológica, pedidos de receta, consulta de paciente, impresión masiva (`pages/prescriptor/`) | 80 propias |
| `PROVEEDORES` | Proveedores del hospital | Órdenes de compra, cotizaciones, listas de precios, certificados de recepción (`pages/proveedor/`) | 79 propias |

Ambos con login propio, auto-registro y recuperación de contraseña. `RECETAS` consume
`VALIDADORES` para elegibilidad y tiene un cliente REST a una API de recetas con
almacenamiento S3 y `x-api-key` (marcado en el código como trabajo reciente, no legacy
original). `PROVEEDORES` no tiene ningún cliente externo: solo procesa archivos subidos.

Se planifican como **pantallas con paridad UI**, no como interfaces.

## Laboratorio — tres frentes distintos

### a) Laboratorios externos / LIS

Todos implementados en `HOSPITAL-BUSINESS/src/.../business/interfaces/implementations/`; los
jobs de `SCHEDULER` solo disparan.

| Contraparte | Implementación | Transporte | Dirección |
|-------------|----------------|------------|-----------|
| Nextlab | `ImpBusInterfaceNextLab.java` (~900 líneas) | **Base SQL Server intermedia**: tablas `GeaCab`/`GeaLin` + stored procedure | Bidireccional |
| DNLab / Centralab | `ImpBusInterfaceDNLab.java` (~943) + `ImpBusRecepcionInterfaceDNLab.java` (~914) | SOAP con **mensajes HL7** como payload (HAPI); recepción también por REST en `WS-HOSPITAL` | Bidireccional |
| Kern | `ImpBusEnvioInterfaceKern.java` (~1.087) + recepción | REST/JSON | Bidireccional |
| Medibase | `ImpBusInterfaceMedibase.java` (~277) | Archivos planos sobre **SMB/CIFS**, ISO-8859-1 | Solo envío |

Son laboratorios a los que el hospital **deriva muestras**; no son otros hospitales.

### b) Autoanalizadores (instrumental)

`HOSPITAL-BUSINESS/src/.../business/interfaces/lab/implementations/ImpBusInterfaceLaboratorio.java`
(~1.476 líneas), transporte en `.../interfaces/lab/communication/`. Cuatro canales
configurables por equipo desde `pages/laboratorio/equipoAreaLabServ/`: **ASTM** sobre TCP
(uni y bidireccional), ASTM sobre **puerto serie**, intercambio por **directorios**, y
**base de datos**. Del lado servidor, `SCHEDULER/src/ar/com/thinksoft/interfaz/lab/general/`
expone `AstmServlet`, `AstmBidirectionalServlet`, `AstmSocketResultServer` y
`SerialPortResultServer`.

**La integración más crítica del conjunto**: si cae, los resultados de los equipos se cargan
a mano.

### c) Imagenología (PACS/DICOM)

`ImpBusInterfaceFuji` (~3.307 líneas), `ImpBusInterfaceImagen` y DTOs `*Quantio`, con
`dcm4che`; endpoints REST para Philips y VisualMedica; job
`InterfaceMensajesXMLGriensuJob`. Frente propio, no relevado.

## Resto de los jobs `Interface*`

| Job | Con qué habla | Transporte |
|-----|---------------|------------|
| `InterfaceComprobanteCitiJob` | Régimen informativo CITI de AFIP hacia contabilidad | Base intermedia |
| `InterfaceFacturacionDatatechJob` · `InterfaceInternacionUPJob` | Datatech (comprobantes y censo de internación) | Base Oracle intermedia |
| `EnvioPacientesDosysJob` | Dosys (dispensación automatizada de farmacia) | REST/JSON, con entrada en `WS-HOSPITAL` |
| `InterfaceInternacionItoizJob` · `*CemicJob` | Migraciones de datos de instalaciones específicas | Varios |

Los `*CemicJob` y `MigraPersonalV8AV9Job` son **migraciones puntuales**, no interfaces
permanentes: candidatos a no portar.
