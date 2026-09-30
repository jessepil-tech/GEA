---
title: Inventario validaciones — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo.validaciones
---

# Validaciones — modo equipo

Las de serv/pers siguen en T4. Acá solo las que T4 marcó diferidas.

| Regla | Mensaje | UI | Api |
|-------|---------|----|-----|
| Generar sin equipo | Debe seleccionar un equipo. (`REQUIRED_EQUIPO`) | en alcance | en alcance |
| Eliminar sin equipo | `EQUIPO_REQUIRED_ERROR` | en alcance | en alcance |
| Consultar sin equipo | `REQUIRED_EQUIPO` | en alcance | en alcance |
| Sin hab vigente del equipo | el mismo mensaje de hab que T4 usa para serv/pers | en alcance | en alcance |
| Sin horario vigente del equipo | observaciones, igual que un personal sin horario | en alcance | en alcance |

Fechas, horas y grupo en cero no se reescriben: siguen las de T4.
