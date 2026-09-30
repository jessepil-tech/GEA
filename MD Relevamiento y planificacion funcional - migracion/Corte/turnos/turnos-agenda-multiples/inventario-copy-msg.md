---
title: Inventario copy — turnos múltiples
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-multiples.copy
---

# Inventario copy

Fuente: `Resources.properties` HOSPITAL_2 + MessageBundle. Web: `turnos-agenda-labels.ts`.

| msg.key / MessageBundle | Valor | Este slice |
|-------------------------|-------|------------|
| `turnos_multiples` | Turnos Múltiples | Turnero + page |
| `agregar` | Agregar | botón 100px |
| `consultar` | Consultar | ya T5 |
| `limpiar_datos` | Limpiar Datos | ya T5 |
| `prestacion` | Prestación | ya |
| `centro_atencion` | Centro Atención | ya |
| `servicio` | Servicio | ya |
| `profesional` | Profesional | ya |
| `equipo` | Equipo | ya; disabled |
| `profesional_equipo` | Profesional/Equipo | col tablas |
| `hora` | Hora | col |
| `duracion` | Duración | col |
| `turnos` | Turnos | header tabla + fecha |
| `asignar_turno` | Asignar Turno | south (reserva, no otorga) |
| `eliminar` | Eliminar | title trash |
| `cambiar_horario` | Cambiar Horario | icono ⇄ **done** hijo |
| `dias_disponibles` | Días Disponibles | west header |
| `desde` / `hasta` | Desde / Hasta | time 45px |
| `feriado`…`no_disponible` | ya T5 | legend |
| `datos_paciente` | ya T5.1 | west ficha |
| `seleccione_centro_de_atencion` | Seleccione el Centro de Atención | popup header |
| `centro_ate` | Centro Atención | label popup |
| `aceptar` / `cancelar` | ya | popup |
| `no_se_encontraron_registros` | ya | emptyMessage |
| `PRESTACION_REQUIRED_INFO` | Debe seleccionar al menos una Prestación. | Agregar |
| `REQUIRED_CONVENIO_ERROR` | Debe seleccionar un Convenio | Consultar (Api ya con punto) |
| `NO_HAY_TURNOS` | No hay turnos | Asignar tabla vacía |
| `CENTRO_ATENCION_REQUIRED_ERROR` | Debe elegir el Centro de Atención. | popup |
| `e_exc_turno_mismo_horario` | El paciente tiene turnos para el mismo horario. | reserva lote |
