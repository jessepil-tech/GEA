---
title: Inventario copy — T6.2 cola reasignar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.copy
---

# Inventario copy — Cola Reasignación de Turnos

Fuente: `Resources.properties` HOSPITAL_2 + `MessageBundle`.  
Web: extender `turnos-agenda-labels.ts` (no inventar). `reasignacionTurnos` ya existe.

| msg.key / constante | Valor | Uso |
|---------------------|-------|-----|
| `reasignacion_turnos` | Reasignación de Turnos | accordion + pageTitle |
| `turnos_a_reasignar` | Turnos A Reasignar | header tabla |
| `centro_atencion` | Centro Atención | north + col |
| `servicio` | Servicio | north + col |
| `profesional` | Profesional | north |
| `equipo` | Equipo | north (disabled) |
| `buscar` | Buscar | title lupa |
| `fecha_desde` | Fecha Desde | north |
| `fecha_hasta` | Fecha Hasta | north |
| `hora_desde` | Hora Desde | north |
| `hora_hasta` | Hora Hasta | north |
| `consultar` | Consultar | title botón |
| `limpiar_datos` | Limpiar Datos | title Limpiar |
| `acciones` | Acciones | col al final (D-TUR-67; HIS first 60) |
| `REASIGNAR_TURNO` | REASIGNAR TURNO | gear |
| `INFORMACION` | INFORMACIÓN | gear |
| `observaciones` | Observaciones | botón + label popup |
| `observaciones_reasignar_turno` | Observaciones Reasignar Turno | header dialog |
| `aceptar` / `cancelar` | Aceptar / Cancelar | popup |
| `fecha` | Fecha | col 64 |
| `hora` | Hora | col 52 |
| `duracion` | Duración | col 52 |
| `paciente` | Paciente | col |
| `profesional_equipo` | Profesional/Equipo | col |
| `prestacion` | Prestación | col |
| `convenio` | Convenio | col |
| `plan_convenio` | Plan Convenio | col |
| `telefono` | Teléfono | col |
| `no_se_encontraron_registros` | No se encontraron registros | empty table |
| `sobreturno` / `cancelado` / `reemplazado` / `no_modificable` | leyenda south | south |
| `imprimir` | Imprimir | south **disabled** (hijo) |
| `exportar_excel` | Exportar Excel | south **disabled** (hijo) |
| `WRONG_INTERVAL_DATE_3` | La fecha hasta debe ser posterior a la fecha desde | toast |
| `WRONG_INTERVAL_HOUR` | El horario hasta debe ser posterior al horario desde | toast |
| `OBSERVACION_AGREGADA_EXITO` | Las observaciones fueron agregadas con éxito. | toast |
