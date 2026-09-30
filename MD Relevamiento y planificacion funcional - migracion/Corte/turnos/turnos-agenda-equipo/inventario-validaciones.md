---
title: Inventario validaciones — combo Equipo de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-24
phase_id: sdd.hospital.turnos-agenda-equipo.validaciones
---

# Validaciones

| Regla | Mensaje | Cuándo |
|-------|---------|--------|
| Hace falta uno de los tres | `Debe seleccionar un servicio, un profesional o un equipo.` | consultar sin servicio, sin profesional y sin equipo |

El combo encendido no agrega un mensaje nuevo. Otorgar reusa las validaciones ya cerradas de Agenda.
