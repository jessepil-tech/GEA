---
title: Inventario copy — turnos repetidos
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-repetidos.copy
---

# Inventario copy

Fuente: `Resources.properties` + MessageBundle. Web: `turnos-agenda-labels.ts`.

| msg.key / MessageBundle | Valor | Este slice |
|-------------------------|-------|------------|
| `TURNOS_REPETIDOS` | TURNOS REPETIDOS | menú |
| `turnos_repetidos` | Turnos Repetidos | header popup + pageTitle |
| `dias` | Días | label checkboxes |
| `domingo`…`sabado` | ya T3/T4 | checkboxes |
| `ctd_turnos` | Ctd. Turnos | input |
| `asignar_turno` | Asignar Turno | popup: **reserva** (no otorga) |
| `volver` | Volver | popup y south |
| `asignar_turnos` | Asignar Turnos | south → infoTurno |
| `turnos_reservados` | Turnos Reservados | header tabla |
| `fecha_hora` | ya | col |
| `personal_equipo` | ya T5.2 | col |
| `cambiar_horario` | title icon | **done** T5.3-b |
| `observaciones` / `observacion` | ya | tabla faltantes |
| `informacion` | ya | header popup error |
| `aceptar` | ya | |
| `NO_SE_ENCONTRARON_TURNOS` | No se encontraron turnos en los días seleccionados | popup |
| `SE_GENERARON_XXX_TURNOS_XXX_SIN_GENERAR` | Se generaron {%1} turnos, {%2} no se encontraron. | popup parcial |
