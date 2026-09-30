---
title: Inventario copy — T6.3 historial turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.copy
---

# Inventario copy — Historial Turnos

Fuente: `Resources.properties` HOSPITAL_2 + `MessageBundle`.  
Web: extender `turnos-agenda-labels.ts` (no inventar). `historialTurnos` ya existe.

| msg.key / constante | Valor | Uso |
|---------------------|-------|-----|
| `historial_turno` | Historial Turnos | accordion + pageTitle + header tabla |
| `centro_atencion` | Centro Atención | north + col + popup |
| `servicio` | Servicio | north + col + popup |
| `profesional` | Profesional | north + popup |
| `equipo` | Equipo | north (disabled) + popup |
| `buscar` | Buscar | title lupa |
| `fecha_desde` | Fecha Desde | north |
| `fecha_hasta` | Fecha Hasta | north |
| `hora_desde` | Hora Desde | north |
| `hora_hasta` | Hora Hasta | north |
| `consultar` | Consultar | botón north (con copy, no icon-only) |
| `informacion_turno` | Información Turno | title ícono + header dialog |
| `fecha` | Fecha | col 50 + popup |
| `hora` | Hora | col 50 + popup |
| `duracion` | Duración | col 50 |
| `estado` | Estado | col 66 + popup |
| `paciente` | Paciente | col |
| `telefono` | Teléfono | col |
| `profesional_equipo` | Profesional/Equipo | col + próximos |
| `cod_prestacion` | Cod Prestación | col |
| `prestacion` | Prestación | col |
| `fecha_modificacion` | Fecha Modificación | col 130 |
| `usuario_modifica` | Usuario Modifica | col 120 |
| `tipo_sol_turno` | Tipo Solicitud Turno | col 80 |
| `tipo_cancelacion_turno` | Tipo Cancelación Turno | col 80 |
| `no_se_encontraron_registros` | No se encontraron registros | empty table + tablas popup |
| `sobreturno` | Sobreturno | leyenda south (`turno-sobreturno`) |
| `cancelado` | Cancelado | leyenda (`suspendido-calendar1`) |
| `reasignado` | Reasignado | leyenda (`turno-reasignado`) |
| `reemplazado` | Reemplazado | leyenda (`turno-reemplazado`) |
| `exportar_excel` | Exportar Excel | south — hijo [`turnos-agenda-historial-export/`](../turnos-agenda-historial-export/) **gate-done** |
| `datos_turno` | Datos Turno | panel popup |
| `preparacion_previa` | Preparación Previa | panel popup |
| `datos_paciente` | Datos Paciente | panel popup |
| `proximos_turnos` | Próximos Turnos | panel popup |
| `requisitos_realizacion` | Requisitos Realización | panel popup |
| `documentacion_requerida` | Documentación Requerida | panel popup |
| `observaciones` | Observaciones | popup |
| `apellido` / `nombre` / `sexo` / `fecha_nacimiento` | HIS | ficha popup |
| `tipo_paciente` | Tipo Paciente | popup |
| `convenio` / `plan_convenio` | Convenio / Plan Convenio | popup |
| `doc_requerido` | Doc. Requerido | cols popup |
| `volver` | Volver | footer dialog |
| `WRONG_INTERVAL_HOUR` | El horario hasta debe ser posterior al horario desde | toast |

Label de estado en grilla: valor persistido `SUSPENDIDO` se **muestra** `CANCELADO` (`getEstadoTurnoLabel`). No inventar copy.
