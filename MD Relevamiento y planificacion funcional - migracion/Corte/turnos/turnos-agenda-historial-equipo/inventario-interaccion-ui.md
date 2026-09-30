---
title: Inventario interacción — equipo en historial
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-historial-equipo.interaccion
---

# Interacción

| Acto | Efecto |
|------|--------|
| Cambiar centro o servicio | recarga el combo con la hab vigente de ese centro |
| Elegir equipo | limpia el profesional. No consulta solo |
| Elegir profesional | limpia el equipo |
| Consultar | lista `hist_turno` con `cod_item_equipo` |
| Exportar Excel | usa el mismo filtro |

Sin centro elegido el combo queda en Todos: la lista de equipos exige centro, igual que Agenda.
