---
title: Plan — turnos repetidos agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos.plan
---

# Plan

| Capa | Decisión (Camino 1 **FIRME**) |
|------|-------------------------------|
| DDL | G1 `ts.tmp_observ_tur_rep` si el port escribe; si no, observaciones en el DTO. Sin ALTER de `turno`. |
| API | POST reserva N; reusa otorgar/liberar T5. |
| Web | Menú gear + popup + vista tablas + infoTurno tabla. |
| Tests | Ampliar `turnos-agenda.spec.ts` |

Orden: G0 inventarios **hecho** → G1 DTO (sin tmp) → G2 API → G3 Gate UI popup+página **antes** template → G4 Web → e2e G5 → smoke G6.

## Cortes internos

| Gate | Exit |
|------|------|
| G0 | Clarify **FIRME** 2026-09-10 + inventarios |
| G1 | Flyway tmp si aplica |
| G2 | POST reserva; mensajes 0/parcial |
| G3 | Gate UI `$popupTurnosRepetidos` + layout north/center/south **antes** de smoke |
| G4 | Web menú → reserva → tablas → otorga/volver |
| G5 | e2e |
| G6 | Smoke ops Francisco — **PASS** 2026-09-11 |

## Fuera

Cambiar horario **done** T5.3-b; múltiples; pre-agenda; equipo; T6 padre; T7.
