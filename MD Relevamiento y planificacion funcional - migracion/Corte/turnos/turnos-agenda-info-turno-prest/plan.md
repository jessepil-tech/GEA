---
title: Plan — prep / requisitos realización infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.plan
---

# Plan

| Capa | Decisión (Camino 1 FIRME) |
|------|-------------------------------|
| DDL | G1 `CREATE` tres tablas `ts` + seed CONS/`80001`. Sin ALTER de `turno`. |
| API | GET en `TurnosAgendaResource`. Handler CQRS. JDBC edad + primera prep + JOIN req. |
| Web | Al abrir dialog: GET → `innerHTML` prep + filas tabla req. Chrome T5.1e sin rehacer. |
| Tests | Ampliar `turnos-agenda.spec.ts` |

Orden: **FIRME** → G0 inventarios (este corte) → **G1 Flyway** → G2 API → G3 Gate UI fill (no template nuevo) → G4 Web → e2e G5 → smoke G6.

## Cortes internos

| Gate | Exit |
|------|------|
| G0 | Clarify **FIRME** + inventarios |
| G1 | Flyway V52 CREATE + V53 seed CONS/`80001` — **done** |
| G2 | GET prep+req; edad; empty OK — **done** |
| G3 | Rellenar chrome xhtml L150–232 **antes** de pedir smoke — **done** |
| G4 | Web: abrir dialog muestra seed — **done** |
| G5 | e2e — **done** (`turnos-agenda.spec.ts`) |
| G6 | Smoke ops Francisco — **PASS** 2026-09-10 |

## Fuera

Print; repetidos concat; equipo; ABM; Resource nuevo; T6 padre.
