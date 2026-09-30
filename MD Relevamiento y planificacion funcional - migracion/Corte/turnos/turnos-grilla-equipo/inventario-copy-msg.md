---
title: Inventario copy — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo.copy
---

# Copy — solo el delta Equipo

El resto del copy es el de T4 ([`turnos-generacion-grilla/inventario-copy-msg.md`](../turnos-generacion-grilla/inventario-copy-msg.md)). Estas claves salen de diferido y entran en este corte.

| Key | Texto | Dónde |
|-----|-------|-------|
| `equipo` | Equipo | radio de las tres hojas |
| `buscar_equipo` | Buscar Equipo | título del diálogo |
| `grp_prest_equipo` | grupo de prestaciones del equipo | combo visible con el radio Equipo |
| `REQUIRED_EQUIPO` | Debe seleccionar un equipo. | generar y consultar |
| `EQUIPO_REQUIRED_ERROR` | Debe seleccionar un equipo. | eliminar |

El toast de fin y la tabla de observaciones no cambian de texto.
