---
title: Inventario copy — M1a centro de atención
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1a-centro.copy
---

# Inventario copy — M1a (G0)

Fuente: `HOSPITAL_2/.../Resources.properties`.

XHTML in-scope: `centroAtencion.xhtml` · `datosCentroAtencion.xhtml` · `buscadorCentroAtencion.xhtml` · `buscadorDeposito.xhtml`.

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `centro_atencion` | Centro Atención | `centroAtencion` | título, north, west tab, campo `(*)` |
| `grp_centro_atencion` | Grupo Centro Atención | `grpCentroAtencion` | datos `(*)` |
| `nombre_centro` | Nombre Centro | `nombreCentro` | datos `(*)` |
| `calle` | Calle | `calle` | datos `(*)` |
| `numero_calle` | Número Calle | `numeroCalle` | datos `(*)` |
| `provincia` | Provincia | `provincia` | datos `(*)` |
| `localidad` | Localidad | `localidad` | datos `(*)` |
| `cod_postal` | Código Postal | `codPostal` | datos |
| `activo` | Activo | `activo` | datos checkbox |
| `virtual` | Virtual | `virtual` | datos checkbox |
| `area_contable` | Area contable | `areaContable` | datos |
| `referencia_domicilio` | Referencia Domicilio | `referenciaDomicilio` | textarea |
| `centro_atencion_dflt` | Centro Atención por Defecto | `centroAtencionDflt` | datos |
| `cod_servicio_dflt` | Servicio por Defecto | `codServicioDflt` | datos |
| `website` | Website | `website` | datos |
| `email` | Correo Electrónico | `email` | datos |
| `telefono` | Teléfono | `telefono` | datos |
| `contacto` | Contacto | `contacto` | datos |
| `deposito_int_dom_dflt` | Depósito Internación Domiciliaria por Defecto | `depositoIntDomDflt` | datos + lupa |
| `segundos_refresh_ate_med` | Segundos Refresco Atención Médica | `segundosRefreshAteMed` | datos `(*)` |
| `segundos_refresh_lab` | Segundos Refresco Laboratorio | `segundosRefreshLab` | datos `(*)` |
| `seg_refresh_imagen` | Segundos Refresco Imagen | `segRefreshImagen` | datos `(*)` |
| `segundos_refresh_enfer` | Segundos Refresco Enfermería | `segundosRefreshEnfer` | datos `(*)` |
| `segundos_refresh_recep` | Segundos Refresco Recepción | `segundosRefreshRecep` | datos `(*)` |
| `seg_refresh_otros` | Segundos Refresco Otros | `segRefreshOtros` | datos `(*)` |
| `seg_inact_guardia` | Segundos Inactividad Guardia | `segInactGuardia` | datos `(*)` |
| `seg_refresh_turno` | Segundos Refresco Turno | `segRefreshTurno` | datos `(*)` |
| `gs1_cod_centro_ate` | Código GS1 Centro Atención | `gs1CodCentroAte` | datos |
| `gs1_prefix_centro_ate` | Prefijo GS1 Centro Atención | `gs1PrefixCentroAte` | datos |
| `cantidad_hs_track_pac` | Cantidad Horas Seguimiento Paciente | `cantidadHsTrackPac` | datos `(*)` |
| `ctd_dias_venc_os_tran` | Cantidad Días Vencimiento Orden Servicio Transitoria | `ctdDiasVencOsTran` | datos `(*)` |
| `ctd_mtos_modif_evolucion_int` | Ctd. Minutos Modifica Evolución Internado | `ctdMtosModifEvolucionInt` | datos |
| `interna_pacientes` | Interna Pacientes | `internaPacientes` | datos checkbox |
| `servicio_dflt_int` | Servicio por Defecto Internados | `servicioDfltInt` | datos |
| `hora_corte_pension_fact_int` | Hora de Corte Pension Fact. Int. | `horaCortePensionFactInt` | datos time |
| `nro_centro` | Número Centro | `nroCentro` | datos |
| `ctd_prest_prt_ord_serv_amb` | Cantidad Prestaciones Que Imprimen Orden Servicio Ambulatoria | `ctdPrestPrtOrdServAmb` | datos `(*)` |
| `cod_centro_interface_pac_img` | Cod. Centro Interface PAC Img. | `codCentroInterfacePacImg` | datos (no virtual) |
| `cod_centro_interface_lab` | Cod. Centro Interface Lab. | `codCentroInterfaceLab` | datos (no virtual) |
| `mensaje_indica_droga_no_hab` | Mensaje Indica Droga No Habilitada | `mensajeIndicaDrogaNoHab` | datos (no virtual) |
| `mensaje_indica_droga_uso_restrin` | Mensaje Indica Droga Uso Restringido | `mensajeIndicaDrogaUsoRestrin` | datos (no virtual) |
| `mensaje_indica_item_no_hab` | Mensaje Indica Ítem No Habilitado | `mensajeIndicaItemNoHab` | datos (no virtual) |
| `tipo_triage` | Tipo Triage | `tipoTriage` | datos (no virtual) |
| `X3_NIVELES` / `X5_NIVELES` | 3 NIVELES / 5 NIVELES | `x3Niveles` / `x5Niveles` | combo triage |
| `unifica_hora_centro_quirurgico_quirofano` | Unifica Hora de Centro Quirúrgico y Quirófano | `unificaHoraCentroQuirofano` | datos (no virtual) |
| `traslada_med_cambio_cama` | Traslada Medicación en Cambio de Cama | `trasladaMedCambioCama` | datos (no virtual) |
| `nombre_logo_prt_pulsera` | Nombre Logo Impresora Pulsera | `nombreLogoPrtPulsera` | datos (no virtual) |
| `servicio_prescribe_web_dflt` | Servicio Prescribe Web por Defecto | `servicioPrescribeWebDflt` | datos (no virtual) |
| `lugar_entrega_receta_digital` | Lugar de Entrega de Receta Digital | `lugarEntregaRecetaDigital` | datos (no virtual) |
| `ctrl_ate_receta_web` | Controla atención en receta web | `ctrlAteRecetaWeb` | datos (no virtual) |
| `tipo_int_centro_dflt_bebe` | Tipo de Internación por Servicio Centro por Defecto Binomios | `tipoIntCentroDfltBebe` | título bloque |
| `tipo_admision` | Tipo Admisión | `tipoAdmision` | datos `(*)` no virtual |
| `tipo_internacion` | Tipo Internación | `tipoInternacion` | datos `(*)` no virtual |
| `servicio` | Servicio | `servicio` | datos `(*)` no virtual |
| `funcion_internacion` | Función Internación | `funcionInternacion` | datos `(*)` no virtual |
| `HOSPITALARIA` | HOSPITALARIA | `hospitalaria` | combo admisión |
| `deposito` / `deposito_item` | Depósito / Depósito ítem | `deposito` / `depositoItem` | buscador depósito |
| `buscar_deposito` | Buscar Depósito | `buscarDeposito` | dialog lupa |
| `leyenda` | Los campos marcados con (*) son obligatorios | `leyenda` | pie datos |
| `buscar` | Buscar | `buscar` | barra HAB |
| `buscar_centro_atencion` | Buscar Centro Atención | `buscarCentroAtencion` | N/A — dialog absorbido |
| `agregar` | Agregar | `agregar` | debajo de tabla |
| `eliminar` | Eliminar | `eliminar` | ícono de fila |
| `aceptar` | Aceptar | `aceptar` | footer dialog |
| `acciones` | Acciones | `acciones` | columna tabla |
| `desea_eliminar_el_registro` | ¿Desea eliminar el registro? | `deseaEliminarElRegistro` | modal baja |

CRUD toasts (MessageBundle, no msg.key): `Registro insertado con éxito.` · `Registro actualizado con éxito.` · `Registro eliminado con éxito.` · `Debe completar todos los campos requeridos.`

West hojas (mensajes / concepto contable / recepción / logo) no listadas → no copiar labels en M1a.
