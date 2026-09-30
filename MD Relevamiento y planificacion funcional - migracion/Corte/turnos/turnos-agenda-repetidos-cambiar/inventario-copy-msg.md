---
title: Inventario copy — cambiar horario repetidos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.copy
---

# Inventario copy

Fuente: `HOSPITAL_2` `Resources.properties` + `turnosRepetidos.xhtml` L161–382.  
Web: `turnos-agenda-labels.ts`.

| msg.key | Valor | Web |
|---------|-------|-----|
| `cambiar_horario` | Cambiar Horario | `cambiarHorario` (title ⇄) |
| `otros_turnos_disponibles` | Otros turnos disponibles | `otrosTurnosDisponibles` (header dialog) |
| `turnos` | Turnos | `turnos` + fecha `dd/MM/yyyy` (facet tabla) |
| `hora` | Hora | `hora` |
| `duracion` | Duración | `duracion` |
| `centro_atencion` | Centro Atención | `centroAtencion` (ya T5) |
| `servicio` | Servicio | `servicio` (ya T5) |
| `profesional_equipo` | Profesional/Equipo | `profesionalEquipo` (ya T5) |
| `cancelar` | Cancelar | `cancelar` (ya T5) |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` (ya T5) |
| `acciones` | Acciones | `acciones` (ya T5.3) |

Sin toast de éxito HIS (MessageManager solo en error). Error API → toast T5 sin `/500`.
