---
title: Plan — turnos múltiples agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples.plan
---

# Plan

| Capa | Decisión (Camino 1 **FIRME**) |
|------|-------------------------------|
| DDL | Sin Flyway. Sin `tmp_turno` / `tmp_turno_filtro`. |
| API | POST días + POST grilla + POST reserva lote. Otorgar/liberar T5. |
| Web | Turnero + vista carrito/tablas + west calendario + infoTurno tabla. |
| Tests | Ampliar `turnos-agenda.spec.ts` + `AgendaMultiplesEngineTest`. |

Orden: G0 inventarios → G1 DTO → G2 API → G3 Gate UI **antes** template → G4 Web → e2e G5 → smoke G6.

## Cortes internos

| Gate | Exit |
|------|------|
| G0 | Clarify **FIRME** 2026-09-11 + inventarios |
| G1 | DTO filtros / outcome (no tmp) |
| G2 | POST días, grilla, reservar-multiples |
| G3 | Gate UI xhtml **antes** de smoke |
| G4 | Web Turnero → carrito → consultar → asignar → infoTurno |
| G5 | e2e |
| G6 | Smoke ops Francisco **PASS** 2026-09-14 |

## Fuera

Cambiar horario (hijo). Pre-agenda. Consultas. Equipo. T6 padre. T7.
