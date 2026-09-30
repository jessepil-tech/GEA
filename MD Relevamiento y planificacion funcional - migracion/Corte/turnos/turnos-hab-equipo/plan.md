---
title: Plan — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-hab-equipo.plan
---

# Plan

Espejo de hab profesional (`/configuracion/hab-turnos-pers`), con buscador de equipo en lugar de personal.

| Fase | Qué |
|------|-----|
| G0 | Reserva + universo. **Hecho** 2026-09-22, Clarify sin firmar |
| G1 | Template Angular después de la firma. Inventarios ya leídos del xhtml |
| G2 | API list/alta/edición/baja `hab_turnos_equipo_serv` + GET del buscador (lee `equipo_serv_centro`) |
| G3 | Cablear la hoja |
| G4 | IT de vigencia (null, vacía, fin anterior al inicio, porcentaje 100 y 101) |
| G5 | e2e cuando exista el padre |
| G6 | Smoke con una fila real de hab |

Flyway **n/a**. No seed de `hab_turnos_equipo_serv`.
