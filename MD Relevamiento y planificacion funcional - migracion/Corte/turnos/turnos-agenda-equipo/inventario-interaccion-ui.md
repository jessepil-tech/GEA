---
title: Inventario interacción — combo Equipo de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-24
phase_id: sdd.hospital.turnos-agenda-equipo.interaccion
---

# Interacción

| Control | Legacy | Este corte |
|---------|--------|------------|
| `p:selectOneMenu` `id="equipo"` | `actionOnEquipoChange` refresca la agenda | el `select` deja de estar disabled y, al elegir, recarga el día |
| Ítems | `selectItemEquipo` del centro/servicio con hab | equipos con hab vigente, no la tabla entera |
| Columna Profesional/Equipo | `turno.personalEquipo` | el hueco EQUIPO muestra el nombre, no el código |
