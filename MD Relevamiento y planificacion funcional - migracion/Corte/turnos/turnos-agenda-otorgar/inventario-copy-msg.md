---
title: Inventario copy — T5 agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-otorgar.copy
---

# Inventario copy — T5 agenda / otorgar (G0)

Fuente UI: `Resources.properties` (keys lowercase).  
XHTML in-scope v1:

- `turnos/asignacionTurnos/agenda.xhtml`
- `turnos/asignacionTurnos/asignacionTurnos.xhtml` (west turnero: **Agenda** + **Sobreturno**; popups compartidos)
- `turnos/asignacionTurnos/infoTurno.xhtml`

Web destino (G5): `Hospital-Web/src/app/turnos/pages/turnos-agenda-labels.ts` (nombre a confirmar en G5).

| msg.key | Valor típico properties | Constante Web | En UI v1 |
|---------|-------------------------|---------------|----------|
| `agenda` | Agenda | `titleAgenda` | sí |
| `turnero` | Turnero | `titleTurnero` | breadcrumb / shell |
| `paciente` | Paciente | `paciente` | sí (north + panel sur) |
| `estado` | Estado | `estado` | sí |
| `convenio` | Convenio | `convenio` | sí |
| `plan` | Plan | `plan` | sí |
| `plan_convenio` | Plan Convenio | `planConvenio` | sí (info turno) |
| `nro_afiliado` | Nro. Afiliado | `nroAfiliado` | sí |
| `nro_documento` | Nro. Documento | `nroDocumento` | sí |
| `centro_atencion` | Centro Atención | `centroAtencion` | sí |
| `servicio` | Servicio | `servicio` | sí |
| `profesional` | Profesional | `profesional` | sí |
| `equipo` | Equipo | `equipo` | label sí; filtro **diferido** D-TUR-17 |
| `prestacion` | Prestación | `prestacion` | sí |
| `considerar_primera_consulta` | Considerar Primera Consulta | `considerarPrimeraConsulta` | sí |
| `consultar` | Consultar | `consultar` | sí |
| `limpiar_datos` | Limpiar Datos | `limpiarDatos` | sí |
| `limpiar_datos_paciente` | Limpiar datos paciente | `limpiarDatosPaciente` | sí |
| `limpiar_datos_prestacion` | Limpiar datos prestación | `limpiarDatosPrestacion` | sí |
| `limpiar_profesional` | Limpiar profesional | `limpiarProfesional` | sí |
| `buscar` | Buscar | `buscar` | sí |
| `informacion_convenio` | Información Convenio | `informacionConvenio` | sí |
| `informacion_prestacion` | Información Prestación | `informacionPrestacion` | sí |
| `informacion_observaciones` | Información Observaciones | `informacionObservaciones` | sí |
| `informacion_de_busqueda` | Información de Búsqueda | `informacionBusqueda` | sí |
| `validando_elegibilidad` | Validando Elegibilidad | `validandoElegibilidad` | sí (spinner) |
| `turnos` | Turnos | `turnos` | sí (header grilla) |
| `primera_vez` | Primera Vez | `primeraVez` | sí (col) |
| `hora` | Hora | `hora` | sí |
| `fecha` | Fecha | `fecha` | sí |
| `profesional_equipo` | Profesional/Equipo | `profesionalEquipo` | sí |
| `motivo_suspension_reemplazo` | Motivo Suspensión/Reemplazo | `motivoSuspensionReemplazo` | sí (col) |
| `motivo_sobreturno` | Motivo Sobreturno | `motivoSobreturno` | sí (col) |
| `ASIGNAR_TURNO` | Asignar Turno | `asignarTurno` | sí (**menú gear fila**, no botón inline) |
| `LIBERAR` | Liberar | `liberar` | sí (**menú gear fila**) |
| `INFORMACION` | Información | `informacion` | sí (**menú gear fila**) |
| `CONSULTAR_AGENDA` | Consultar Agenda | `consultarAgenda` | sí (otros centros) |
| `reservado` | Reservado | `leyendaReservado` | sí (footer) |
| `sobreturno` | Sobreturno | `sobreturno` | sí (footer + west) |
| `cancelado` | Cancelado | `cancelado` | sí |
| `reemplazado` | Reemplazado | `reemplazado` | sí |
| `inhibido` | Inhibido | `inhibido` | sí |
| `pendiente_liberar` | Pendiente Liberar | `pendienteLiberar` | sí |
| `dias_disponibles` | Días Disponibles | `diasDisponibles` | sí (calendario) |
| `desde` / `hasta` | Desde / Hasta | `desde` / `hasta` | sí (rango calendario) |
| `feriado` | Feriado | `feriado` | sí (leyenda calendario) |
| `disponible` | Disponible | `disponible` | sí |
| `completo` | Completo | `completo` | sí |
| `con_turno` | Con Turno | `conTurno` | sí |
| `no_disponible` | No Disponible | `noDisponible` | sí |
| `datos_paciente` | Datos Paciente | `datosPaciente` | sí (panel sur) |
| `fecha_nacimiento` | Fecha Nacimiento | `fechaNacimiento` | sí |
| `edad` | Edad | `edad` | sí |
| `sexo` | Sexo | `sexo` | sí |
| `convenio_plan` | Convenio/Plan | `convenioPlan` | sí |
| `observaciones` | Observaciones | `observaciones` | sí |
| `telefonos` | Teléfonos | `telefonos` | sí |
| `mails` | Mails | `mails` | sí |
| `primer_turno_disponible_centros` | Primer Turno Disponible (otros centros) | `primerTurnoOtrosCentros` | sí |
| `el_paciente_tiene_turnos_pactados_para_este_dia` | El paciente tiene turnos pactados para este día | `pacienteTurnosHoy` | sí (popup) |
| `el_servicio_no_admite_esa_edad` | El servicio no admite esa edad | `servicioNoAdmiteEdad` | sí |
| `el_personal_no_admite_esa_edad` | El personal no admite esa edad | `personalNoAdmiteEdad` | sí |
| `desea_continuar` | ¿Desea continuar? | `deseaContinuar` | sí |
| `seleccione_motivo_liberacion` | Seleccione motivo de liberación | `seleccioneMotivoLiberacion` | sí |
| `aceptar` | Aceptar | `aceptar` | sí |
| `cerrar` | Cerrar | `cerrar` | sí |
| `cancelar` | Cancelar | `cancelar` | sí |
| `volver` | Volver | `volver` | sí |
| `asignar_turno` | Asignar Turno | `btnAsignarTurno` | sí (infoTurno) |
| `informacion_turno` | Información Turno | `informacionTurno` | sí (dialog header) |
| `buscar_paciente` | Buscar Paciente | `buscarPaciente` | sí (popup) |
| `buscar_convenio` | Buscar Convenio | `buscarConvenio` | sí |
| `buscar_prestacion` | Buscar Prestación | `buscarPrestacion` | sí |
| `buscar_profesional` | Buscar Profesional | `buscarProfesional` | sí |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | sí |
| `econsulta` | eConsulta | `econsulta` | sí (icon prestación) |

## Fuera de v1 (no inventariar en labels.ts salvo placeholder disabled)

West menú diferido: `turnos_multiples`, `consulta_agenda`, `reasignacion_turnos`, `pre_agenda_turnos`, `historial_turno`, tab **Paciente** (ficha → `turnos-agenda-ficha-paciente`).  
Menú fila diferido T6/T7: `REASIGNAR`, `CANCELAR_REASIGNACION`, `TURNOS_REPETIDOS`, `REENVIAR_MAIL`, `IMPRIMIR`.

## MessageBundle (toast — no msg.*)

Ver [`inventario-validaciones.md`](inventario-validaciones.md): `TURNO_ASIGNADO_EXITO`, `TURNO_LIBERADO_EXITO`, etc.
