---
title: Plan — cambiar horario turnos repetidos
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.plan
---

# Plan

| Capa | Decisión (Camino 1 **FIRME**) |
|------|-----------------------------------|
| DDL | Ninguno. Sin ALTER. |
| API | Reusa T5 GET grilla `LIBRES` + POST reservar + POST liberar. Sin Resource nuevo. |
| Web | Habilitar ⇄ T5.3 + dialog Origin 1200×550 + use-case orquesta. |
| Tests | Ampliar `turnos-agenda.spec.ts` |

Orden: Clarify **FIRME** → inventarios G0 → Gate UI dialog **antes** de template → Web → e2e → smoke G6.

## Cortes internos

| Gate | Exit |
|------|------|
| G0 | Clarify **FIRME** 2026-09-11 + inventarios **hecho** |
| G1 | N/A Flyway |
| G2 | N/A endpoint nuevo (reusa T5; IT existentes cubren reserva/libera) |
| G3 | Gate UI `$popupCambiarTurno` **antes** de habilitar icono / smoke — **done 2026-09-11** |
| G4 | Web icono → popup → rowSelect → tablas — **done 2026-09-11** |
| G5 | e2e — **PASS 2026-09-11** |
| G6 | Smoke ops Francisco — **PASS 2026-09-11** |

## Fuera

Calendario (WAIVE). Múltiples. Pre-agenda. Equipo. T6 padre. T7.
