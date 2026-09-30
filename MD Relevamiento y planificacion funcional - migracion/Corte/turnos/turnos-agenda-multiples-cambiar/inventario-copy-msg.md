---
title: Inventario copy — cambiar horario turnos múltiples
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.copy
---

# Inventario copy

Fuente: `HOSPITAL_2` `Resources.properties` + `turnosMultiples.xhtml` L301–575.  
Web: `turnos-agenda-labels.ts` (ya T5.3-b / T5.4).

| msg.key / MessageBundle | Valor | Web |
|-------------------------|-------|-----|
| `cambiar_horario` | Cambiar Horario | `cambiarHorario` (title ⇄; quitar tooltip diferido) |
| `otros_turnos_disponibles` | Otros turnos disponibles | `otrosTurnosDisponibles` (header dialog) |
| `turnos` | Turnos | `turnos` + fecha `dd/MM/yyyy` (facet tabla) |
| `hora` | Hora | `hora` |
| `duracion` | Duración | `duracion` |
| `centro_atencion` | Centro Atención | `centroAtencion` (ya T5) |
| `servicio` | Servicio | `servicio` (ya T5) |
| `profesional_equipo` | Profesional/Equipo | `profesionalEquipo` (ya T5) |
| `cancelar` | Cancelar | `cancelar` (ya T5) |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` (ya T5) |
| `ERROR` | Error | toast si no se resuelve el filtro (HIS) |
| `LIMITE_TURNO_PERSONAL` | Se alcanzo la cantidad maxima de turnos por dia para este personal en el servicio. | toast |
| `LIMITE_TURNO_EQUIPO` | Se alcanzo la cantidad maxima de turnos por dia para este equipo en el servicio. | toast |
| `LIMITE_TURNO_SERVICIO` | Se alcanzo la cantidad maxima de turnos por dia para este servicio centro. | toast |
| `LIMITE_TURNO_CONVENIO_PORCENTAJE` | Ha superado el porcentaje de turnos solicitados para su convenio. | toast |
| `LIMITE_TURNO_CONVENIO_CANTIDAD` | Ha superado la cantidad de turnos solicitados para su convenio. | toast |
| `LIMITE_TURNO_PLAN_CONVENIO_PORCENTAJE` | Ha superado el porcentaje de turnos solicitados para su plan convenio. | toast |
| `LIMITE_TURNO_PLAN_CONVENIO_CANTIDAD` | Ha superado la cantidad de turnos solicitados para su plan convenio. | toast |
| `TURNO_OTORGADO_ERROR` | El Turno no pudo ser otorgado. | toast (`modificable` false) |
| `HORA_INVALIDA_ERROR` | La hora debe ser posterior a la hora actual. | toast |

Sin toast de éxito HIS (MessageManager solo en error). Error GET → toast T5 sin `/500`.  
Popup **sin** columna prestación (el de repetidos tampoco).
