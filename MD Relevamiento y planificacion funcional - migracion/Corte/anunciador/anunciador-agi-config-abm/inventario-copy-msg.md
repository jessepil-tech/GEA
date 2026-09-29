---
title: Inventario copy — P3 ABM Anunciador / Terminal AG
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.anunciador-agi-config-abm.copy
---

# Inventario copy — P3 ABM (G0)

Fuente UI: `HOSPITAL_2/.../Resources.properties` (keys lowercase salvo combos `APELLIDO_NOMBRE` / `DOCUMENTO` / `DOCUMENTO_ALTERADO`).

XHTML in-scope v1:

- `pages/configuracion/anunciador/anunciador.xhtml`
- `pages/configuracion/anunciador/datosAnunciador.xhtml`
- `pages/configuracion/anunciador/configuracionAvanzada.xhtml`
- `pages/configuracion/anunciador/anunciadorAmbienteAmb.xhtml`
- `pages/configuracion/terminalAutogestion/terminalAutogestion.xhtml`
- `pages/configuracion/terminalAutogestion/datosTerminalAutogestion.xhtml`
- `pages/configuracion/terminalAutogestion/opcionTerminalAutogestion.xhtml`
- `pages/configuracion/diccionarioAnunciador.xhtml`
- buscadores: `pages/buscadores/buscadorAnunciador.xhtml` · `buscadorTerminalAg.xhtml` · `buscadorAmbienteAmb` (popup vínculo)

Fuera de v1 (copy no entra): `anunciadorServ` / `anunciadorTriage` / `anunciadorEspServ` · `terminalTriage*` · pack logos.

Web destino (C4): `Hospital-Web` constantes `*-labels.ts` por pantalla (nombres a fijar en C4). Lista actual `/anunciadores` **no** es el copy de este CU (es piloto “abrir display”).

## Chrome / acciones comunes

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `anunciador` | Anunciador | `anunciador` | título, north, west tab, campo (*) |
| `buscar` | Buscar | `buscar` | north + icono lupa |
| `agregar` | Agregar | `agregar` | west acciones / grillas |
| `eliminar` | Eliminar | `eliminar` | west + filas |
| `aceptar` | Aceptar | `aceptar` | south |
| `cancelar` | Cancelar | `cancelar` | popups |
| `acciones` | Acciones | `acciones` | west south header / cols |
| `leyenda` | Los campos marcados con (*) son obligatorios | `leyendaObligatorios` | footer datos + config avanzada |
| `no_se_encontraron_registros` | No se encontraron registros | `sinRegistros` | grillas |
| `confirmacion` | Confirmación | `confirmacion` | header modal baja |
| `desea_eliminar_el_registro` | ¿Desea eliminar el registro? | `deseaEliminarRegistro` | terminal, opciones, ambientes, diccionario |
| `desea_eliminar_anunciador` | ¿Desea eliminar el Anunciador? | `deseaEliminarAnunciador` | **solo** baja anunciador (`window.confirm`) |
| `modificar` / `editar` | Modificar / Editar | `modificar` / `editar` | diccionario / opciones |
| `centro_atencion` | Centro Atención | `centroAtencion` | terminal, ambientes, opciones |

## Anunciador — datos

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `titulo` | Título | `titulo` | (*) |
| `ctd_min_max_muestra` | Cantidad de Minutos Máximos Muestra | `ctdMinutosMaxMuestra` | no (*) |
| `ctd_min_max_muestra_help` | Cantidad de minutos máximos que se muestra un Paciente en el Anunciador | `ctdMinutosMaxMuestraHelp` | `title` del label |
| `URL` | URL | `url` | readonly (display) |
| `buscar_anunciador` | Buscar Anunciador | `buscarAnunciador` | dialog 1200×550 |

## Anunciador — config avanzada

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `configuracion_avanzada` | Configuración Avanzada | `configuracionAvanzada` | west menuitem |
| `general` | General | `general` | sección |
| `anunciador_voz` | Anunciador con Voz | `anunciadorVoz` | checkbox |
| `anunciador_multimedia` | Anunciador con Multimedia | `anunciadorMultimedia` | checkbox |
| `mostrar_profesional` | Mostrar Profesional | `mostrarProfesional` | checkbox (ocupación) |
| `tipo_multimedia` | Tipo de Multimedia | `tipoMultimedia` | combo IMAGEN / Video (labels hardcode BB: `Imagen`, `Video`) |
| `url_multimedia` | URL Multimedia | `urlMultimedia` | deshabilitado si no multimedia |
| `lista_pacientes_cola` | Lista de Pacientes en Cola | `listaPacientesCola` | sección |
| `ctd_char` | Ctd. de Caracteres | `ctdChar` | dos campos (*) cola + lugar |
| `tipo_llamado_paciente` | Tipo Llamado Paciente | `tipoLlamadoPaciente` | (*) |
| `APELLIDO_NOMBRE` | APELLIDO NOMBRE | `tipoLlamadoApellidoNombre` | combo |
| `DOCUMENTO` | DOCUMENTO | `tipoLlamadoDocumento` | combo |
| `DOCUMENTO_ALTERADO` | DOCUMENTO ALTERADO | `tipoLlamadoDocumentoAlterado` | combo |
| `lugar_atencion` | Lugar Atención | `lugarAtencion` | sección |
| `logo` / `fondo` | Logo / Fondo | `logo` / `fondo` | secciones 50/50 |
| `sin_imagen` | Sin Imagen | `sinImagen` | placeholder 160×160 |
| `cargar_logo` / `cargar_fondo` | Cargar Logo / Cargar Fondo | `cargarLogo` / `cargarFondo` | fileUpload |
| `quitar_logo` | Quitar Logo | `quitarLogo` | |
| `restablecer_fondo` | Restablecer Fondo | `restablecerFondo` | |
| `restablecer_valores` | Restablecer Valores | `restablecerValores` | south |
| `desea_restablecer_valores` | ¿Desea Restablecer a los valores por defecto?. La configuración actual se perderá. | `deseaRestablecerValores` | `window.confirm` |
| `tamano_no_valido` | Tamaño No Válido | `tamanoNoValido` | fileUpload |
| `archivo_no_valido` | Archivo No Válido | `archivoNoValido` | fileUpload |

## Anunciador — ambientes (vínculo)

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `ambiente` | Ambiente | `ambiente` | west menuitem + col |
| `sector_ambulatorio` | Sector Ambulatorio | `sectorAmbulatorio` | col |
| `agregar_ambiente` | Agregar Ambiente | `agregarAmbiente` | header dialog 1200×550 |

## Terminal AG

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `terminal_auto_gestion` / `term_auto_gest` | Terminal Auto Gestión | `terminalAutoGestion` | título / north / west (HIS usa ambas keys; Web **una** constante) |
| `opcion_auto_gest` | Opción Auto Gestión | `opcionAutoGestion` | west + popup |
| `cant_opciones` | Cantidad de Opciones | `cantOpciones` | (*) |
| `ambiente_ambulatorio` | Ambiente Ambulatorio | `ambienteAmbulatorio` | (*) |
| `imprime_ord_servicio` | Imprime Orden Servicio | `imprimeOrdServicio` | checkbox |
| `url` | URL | `url` | (lowercase; distinto de `URL` anunciador) |
| `activo` | Activo | `activo` | checkbox datos terminal |

## Opciones terminal

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `numero_opcion` | Número Opción | `numeroOpcion` | (*) |
| `leyenda_opcion_terminal_autogestion` | Leyenda Opción Terminal Autogestión | `leyendaOpcion` | (*) |
| `tipo_opcion_auto_gest` | Tipo Opción Auto Gestión | `tipoOpcion` | (*) |
| `ESPERA_TRIAGE` | ESPERA TRIAGE | `tipoEsperaTriage` | combo |
| `AUTO_RECEPCION` | AUTO RECEPCIÓN | `tipoAutoRecepcion` | combo |
| `ESPERA_RECEPCION` | ESPERA RECEPCIÓN | `tipoEsperaRecepcion` | combo |
| `prioridad_ticket` | Prioridad Ticket | `prioridadTicket` | (*) |
| `prefijo_ticket` | Prefijo Ticket | `prefijoTicket` | (*) |
| `titulo_ticket` | Título Ticket | `tituloTicket` | opcional |
| `leyenda_ticket` | Leyenda Ticket | `leyendaTicket` | opcional |
| `opcion_terminal_triage_espera` | Opción Terminal Triage Espera | `terminalTriageEspera` | (*) condicional |
| `recepcion` | Recepción | `recepcion` | (*) condicional |
| `activa` | Activa | `activa` | checkbox |

## Diccionario

| msg.key | Valor properties | Constante Web | En UI v1 |
|---------|------------------|---------------|----------|
| `diccionario_anunciador` | Diccionario del Anunciador | `diccionarioAnunciador` | pageTitle HIS 10212 |
| `palabra` | Palabra | `palabra` | col + form |
| `equivalente` | Equivalente | `equivalente` | col + form |
| `escuchar` | Escuchar | — | **fuera** (`rendered="false"` + script comentado) |

## Toasts MessageBundle (no `msg.*`)

Textos inline en `MessageBundle.java` — UI C4 + API C1–C3:

| Constante | Texto |
|-----------|--------|
| `ROW_INSERT_INFO` | Registro insertado con éxito. |
| `ROW_UPDATE_INFO` | Registro actualizado con éxito. |
| `ROW_DELETE_INFO` | Registro eliminado con éxito. |
| `ROWS_INSERT_INFO` | Registros insertados con éxito. |
| `ROW_DELETE_ERROR` | El registro no pudo ser eliminado. |
| `DEBE_COMPLETAR_LOS_CAMPOS_REQUERIDOS` | Debe completar los campos requeridos. |

## Verify

Tabla copy ↔ `*-labels.ts` en [verify-report.md](verify-report.md) al cerrar C4.
