---
title: Inventario — los 67 jobs de SCHEDULER por dominio
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.relevamiento-procesos-programados.inventario
---

# Los 67 jobs, agrupados por dominio

Hallazgo, veredictos por circuito y lo indeterminado: [`README.md`](README.md).
Origen: `Hospital-Legacy/SCHEDULER/src/ar/com/thinksoft/scheduler/jobs/`.

**Frecuencia:** omitida a propósito en todas las filas. No está en el código — vive en
`TS.TAREA_PROGRAMADA` junto con el flag `ACTIVA`, en producción (README § lo que no se puede
saber). Las dos excepciones están en la sección de técnicos.

Columna **efecto**: `negocio` = crea o modifica datos que un usuario ve · `notifica` = solo
mensajes · `externo` = intercambio con un tercero · `derivado` = tablas de consulta o
reporte · `técnico` = infraestructura.

## Turnos y mensajería de turnos

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `MigrarTurnoVencidoJob` | Archiva turnos vencidos **y** purga cola de recepción/triage y llamados del anunciador; genera avisos de reprogramación | `TS.TURNOS.f_migra_turno_vencido` → `turno_vencido`, `mensaje_turno_vencido`, borra `ts.turno` y `ts.mensaje_turno`, desvincula `cola_espera_serv_amb` · `det_indica_prest_int` · `atencion_int` | **negocio** |
| `CheckHabTurnosJob` | Recalcula vigencia de habilitaciones y los flags `atiende_turnos` | `TS.TURNOS.f_check_hab_turnos` → `hab_turnos_*` (`vigente`), `servicio_centro` · `personal_servicio` · `equipo_serv_centro` (`atiende_turnos`) | **negocio** |
| `ProcesarSmsJob` | Procesa la respuesta del paciente: confirma el turno o **lo libera** | `TS.ENVIO_MAIL_SMS.f_procesar_sms` → `ts.turno.confirmado_por_sms`, `f_libera_turno_pac(…,'SMS')`, `sms_recibido.procesado` | **negocio** |
| `RecepcionSmsJob` | Lee los gateways y registra los SMS entrantes | `sms_recibido`, lee `server_sms` | negocio (entrada) |
| `MailTurnoJob` | Despacha los mensajes de turno y de turno vencido por mail y SMS, por centro. T7 [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) **gate-done** Camino 1 | `mensaje_turno`, `mensaje_turno_vencido` (marca `fecha_hora_envio`) | notifica |

## Recepción ambulatoria y ambientes

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `GeneracionOcupacionAmbienteAmbJob` | Genera la ocupación de ambientes **del día siguiente** desde las reservas recurrentes, sin pisar lo cargado | `TS.INTERFACES.f_genera_ocupacion_amb(sysdate+1,'N')` → `ocupacion_ambiente_amb`, `det_ocupacion_ambiente_amb` | **negocio** |
| `AtencionAutomaticaColaEsperaAmbJob` | Cierra atenciones ambulatorias abiertas — **solo para un cliente**, y su *named query* no existe en el repositorio | `pfCierraAtencionesAmb` (sin mapeo) | descartado |
| `InterfaceMigrarPacientesAmbCemicJob` | Migra pacientes ambulatorios de una instalación específica | no determinado | externo |

## Notificaciones (los *senders* transversales)

Son la plomería por donde salen los mensajes de **todos** los dominios: se cierran una vez y
sirven a todos. Sin ellos, cualquier circuito que notifique queda mudo aunque su pantalla
muestre el mensaje como generado.

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `EnvioMailsJob` | Sender SMTP genérico: envía la cola y marca enviado o error; loguea adjuntos perdidos | `envio_mail`, `mail_envio_para`, `archivo_anexo_mail`, `log_envio_mail_adj` | notifica |
| `EnvioSmsJob` | Sender SMS por HTTP contra el gateway (con rama de URL por cliente) | `envio_sms`, lee `server_sms` | notifica |
| `EnvioWhatsappJob` | Envía los WhatsApp pendientes por POST JSON | `mensaje_whatsapp`, `envio_whatsapp`, lee `param_general` | notifica |
| `EnvioMailsComprobantesJob` | Envía comprobantes al paciente; si no hay mail activo, desmarca | lee `comprobante`, `mail_persona` | notifica |
| `EnvioMailsOrdLabPacJob` | Envía informes de laboratorio confirmados al paciente | lee `ord_lab_pac`, `mail_persona` | notifica |
| `MailImagenJob` | Manda la URL de la imagen de estudios sin informe y marca para no reenviar | `det_atencion_amb` | notifica |
| `MensajeDemoraReqAdiCirugiaJob` | Avisa demora en requerimientos adicionales de cirugía | `ts.ENVIO_MAIL_SMS.f_envio_mensaje_demora_req_adi` | notifica |
| `MensajeSolHousekeepingJob` | Avisa solicitudes de housekeeping | `ts.ENVIO_MAIL_SMS.f_msje_sol_housekeeping` | notifica |
| `ComprasEnvioMailFechaEntregaVencidaJob` | Avisa órdenes de compra con entrega vencida | no determinado | notifica |

## Laboratorio

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `InsertarDeterminacionLaboratorioJob` | Inserta determinaciones faltantes | `TS.LABORATORIO.f_insert_determinacion_lab` | negocio |
| `InsertarDeterminacionServicioJob` | Inserta determinaciones por servicio | `TS.LABORATORIO.f_insert_determinacion_serv` | negocio |
| `InterfaceLaboratorioEnvioJob` | Envía peticiones pendientes por equipo de área | lee `equipo_area_lab_serv` | externo |
| `InterfaceLaboratorioRecepcionJob` | Recepciona resultados de los equipos | no determinado | externo |
| `SincroInterfaceLaboratorioJob` | Sincroniza las tablas de la interfaz por equipo | no determinado | externo |
| `InterfaceNextlabJob` · `InterfaceNextlabRecepcionJob` | Envía pacientes y recibe informes de Nextlab | `TS.INTERFACES.f_recibir_informe_nextlab` | externo |
| `InterfaceDNLABJob` | Envía pacientes a DNLAB | no determinado | externo |
| `InterfaceKernJob` | Envía pacientes, anulaciones y reprocesa muestras | `mensaje_kern` | externo |
| `InterfaceMedibaseJob` | Procesa la interfaz Medibase | `TS.INTERFACES.f_interface_medibase` | externo |

## Farmacia

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `ActualizacionAlfabetaJob` | Actualización automática del vademécum — **descarga y no procesa** ([integraciones](../relevamiento-integraciones-externas/README.md) § hallazgo 4) | ninguno efectivo | inactivo de hecho |
| `GeneracionPedidoAutomaticoJob` | Genera el pedido automático de cada depósito | `ts.FARMACIAS.f_genera_pedido_automatico`, lee `deposito` | negocio |
| `InterfaceCostoPrecioVtaHospitalJob` | Migra costos y precios de venta no migrados con vademécum | `costo_item`, `precio_vta_item`, `item_farm_mf` | negocio |
| `InterfaceItemExternoJob` | Migra ítems externos al catálogo | no determinado | externo |
| `InterfaceProcesarMovStockItoizJob` | Procesa consumos de stock de un tercero | `TS.INTERFACE_COMPRAS_ITOIZ.f_procesar_consumos_itoiz` | externo |
| `InterfaceMigrarMovStockCemicJob` | Migra movimientos de stock de una instalación | no determinado | externo |
| `EnvioRecetaPacWsJob` | Envía por web service las recetas pendientes | lee `receta_pac`, `det_receta_item_pac` | externo |
| `EnvioRecetaPacHmsJob` | Envía recetas a HMS — el código anota que **no se programa** y el envío está comentado | — | probablemente inactivo |

## Compras

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `DetRecepcionCompra` | Recalcula el detalle de recepciones | `ts.COMPRAS.f_det_recepcion_compra` | negocio |
| `EnvioBionexoJob` | Genera y envía solicitudes de cotización y altas de producto | no determinado | externo |
| `RecepcionBionexoJob` | Recibe y procesa cotizaciones (procesado de proveedores **comentado**) | no determinado | externo |
| `GeneraNecesidadCompraHMSJob` | Genera necesidades y actualiza autorizaciones y órdenes | `TS.INTERFAZ_HMS.f_gen_necesidad_compra_HMS` y dos más | negocio |
| `InterfaceNecesidadCompraJob` | Envía necesidades y recepciones pendientes al WS de un financiador | no determinado | externo |
| `InterfaceRecepcionCompraHospitalJob` · `…CemicJob` · `…ItoizJob` | Migran recepciones de compra no migradas (tres variantes por instalación) | lee `recepcion_compra`; la de Itoiz vía `f_migrar_recep_compra_itoiz` | externo |

## Facturación, liquidación y contabilidad

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `ValidarComprobantesJob` | Valida comprobantes contra el facade de factura electrónica | no determinado | externo |
| `InterfaceComprobanteCitiJob` | Envía comprobantes al régimen informativo CITI | no determinado | externo |
| `InterfaceFacturacionDatatechJob` | Procesa comprobantes de la interfaz Datatech | `TS.INTERFACE_FACTURACION_DTT.f_procesar_comprobantes_dtt` | externo |
| `LiquidacionComprobantesJob` · `LiquidacionComprobantesFactJob` | Liquidan comprobantes pendientes de afectación y de facturación | `ts.liquidacion_honorario.f_liquida_comp_pendientes_{afec,fact}` | negocio |
| `GenLotePresenDevenEntLiqHonJob` | Genera el lote de presentación/devengamiento por entidad | `ts.liquidacion_honorario.f_gen_lote_pre_dev_ent_liq_hon` | negocio |
| `GeneracionOrdenLiqHonEntidadJob` | **No-op**: la línea de negocio está comentada | — | código muerto |
| `GeneracionAsientoContableJob` | Genera asientos de cobranzas, ventas, honorarios, recepciones y órdenes de pago | no determinado | negocio |
| `MigrarAsientosContablesJob` | Migra asientos de forma programada | no determinado | negocio |
| `CorridaConsultasJob` | Consolida cinco procesos para reportes | `ejecucion_consultas`, `consulta_liquida_hon`, `consulta_analiticos` | derivado |

## Internación, cirugía e imagenología

| Job | Qué hace | Escribe | Efecto |
|-----|----------|---------|--------|
| `FinalizarVigenciaDetIndicacionJob` | Finaliza vigencia de detalles de indicación médica | `ts.INDICACION_MEDICA.f_finalizar_vigencia_det_ind` | **negocio** |
| `EnvioPacientesDosysJob` | Envía internados al sistema de dispensación y marca procesado o error | `migra_internacion` | externo |
| `InterfaceInternacionItoizJob` · `InterfaceInternacionUPJob` · `InterfaceMigrarInternacionJob` · `…CemicJob` · `InterfaceMigrarAltaInternacionCemicJob` | Migran censo, internaciones y altas (variantes por instalación) | `f_migrar_internaciones_itoiz`, `f_migra_internacion`, `f_migrar_internacion_cemic`, lee `ts_censo` | externo |
| `ConfirmaParteAutomaticamenteJob` | Confirma automáticamente partes/boletines operatorios | no determinado | **negocio** |
| `InterfaceMensajesXMLGriensuJob` | Procesa mensajes XML de la interfaz de imágenes | no determinado | externo |
| `InterfaceDerechosPensionesJob` | Procesa la interfaz de derechos y pensiones | `TS.INTERFACES.f_process_interface_derpen` | externo |

`FinalizarVigenciaDetIndicacionJob` es el mismo patrón que `CheckHabTurnosJob`: un
vencimiento que **solo se materializa si el job corre**. No afecta a los circuitos cerrados
hoy, pero hay que relevarlo cuando se aborde indicación médica.

## Técnicos (fuera del alcance funcional)

| Job | Qué hace | Frecuencia |
|-----|----------|-----------|
| `SchedulerControllerJob` | Meta-job: lee `TAREA_PROGRAMADA` y programa o desprograma los demás | **60 min** (en código) |
| `BorradoArchivosTmpJob` | Borra temporales de más de 1 minuto, con lista de exclusión | **4 h** (en código) |
| `ErroresInterfaceTSJob` | Monitoreo: si hay errores en `error_interfaces_ts`, manda mail de estado | de `TAREA_PROGRAMADA` |
| `MigraPersonalV8AV9Job` | Migración interna de versión del padrón de personal | de `TAREA_PROGRAMADA` |

`SchedulerControllerJob` se descarta del alcance funcional, **pero su lógica hay que
replicarla** si la plataforma nueva necesita tareas programadas configurables en base.
`MigraPersonalV8AV9Job` es probablemente de un solo uso ya cumplido: decidir mirando `ACTIVA`.

## Los otros 15 jobs (fuera de `SCHEDULER`)

| Proyecto | Jobs | Nota |
|----------|-----:|------|
| `ANMAT` | 6 | Trazabilidad; incluye infraestructura de scheduler duplicada |
| `AGI` | 2 | **`LlamadorAnunciadorJob`** + borrado de temporales propio |
| `AGH` · `AGP` · `HOS-APP` · `HOSPITAL_2` · `WS-HOSPITAL` · `RECETAS` · `PROVEEDORES` | 1 cada uno | Relevar con su circuito |

## Nota de método

Los cuerpos PL/SQL de este análisis se leyeron de
`Hospital-Legacy/tools/relevamiento/out/plsql/package_body/` (**326 packages capturados de
Oracle**), no de `RDBMS/`, que solo tiene scripts versionados por sprint y deja huecos. Para
cualquier pregunta sobre qué hace una función del legacy, esa captura es la fuente; si falta
un package ahí, entonces sí hay que ir a la base.
