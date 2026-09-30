---
title: Plan — Turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.plan
---

# Plan

Espejo de turnos por servicio, con el buscador de equipo que ya usa la hab.

| Fase | Qué |
|------|-----|
| G0 | Reserva + universo. **Hecho** 2026-09-23, Clarify FIRME |
| G1 | Completar inventarios y template Angular después de la firma |
| G2 | API list/alta/edición/baja de grupo, prestaciones, horario y días |
| G3 | Cablear la hoja bajo TURNOS |
| G4 | Casos de vigencia (null, copiar anterior, día sin hora) |
| G5 | e2e del viaje Buscar → grupo → prestación → horario |
| G6 | Smoke con una fila real sobre `EQDEMO1001` |

Flyway **n/a**. No seed de `grp_prest_tur_equipo` ni de las tablas hijas.
