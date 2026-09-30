---
title: Inventario validaciones — equipo en historial
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-historial-equipo.validaciones
---

# Validaciones

Equipo no es obligatorio. Vacío es Todos.

Si la hora desde es posterior a la hora hasta, sigue `WRONG_INTERVAL_HOUR`.

Elegir un equipo limpia el profesional. Elegir un profesional limpia el equipo.
