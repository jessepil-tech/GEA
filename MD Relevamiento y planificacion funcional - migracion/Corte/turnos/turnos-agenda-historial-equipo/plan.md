---
title: Plan — Equipo en Historial de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-historial-equipo.plan
---

# Plan

1. Universo: solo el combo de `historialTurno.xhtml`. Hecho.
2. Rama `dev/tur-historial-equipo`. Hecho. Sin push.
3. Gate UI: habilitar el `select` que ya está. Hecho en la rama.
4. Api: `codItemEquipo` en el SELECT de `ts.hist_turno`. La lista de equipos es la de Agenda.
5. Web: el combo deja de estar `disabled`. Elegir equipo limpia el profesional.
6. Evidencia: viaje, fila `9676753`, acceso y p95.
