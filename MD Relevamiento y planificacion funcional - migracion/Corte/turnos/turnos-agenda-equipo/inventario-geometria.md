---
title: Inventario geometría — combo Equipo de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-24
phase_id: sdd.hospital.turnos-agenda-equipo.geometria
---

# Geometría

North de `agenda.xhtml`, misma fila que Profesional.

| Control | Legacy | Web hoy |
|---------|--------|---------|
| Label | `msg.equipo` + `:` | `Equipo:` ya está |
| Combo | `styleClass="InputWid100"` | `select` full, **disabled**, sin opciones |

No se cambia la fila ni el ancho. Se habilita el control que ya ocupa esa celda.
