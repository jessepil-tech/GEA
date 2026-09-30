---
title: Plan — cambiar horario turnos múltiples
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.plan
---

# Plan

| Capa | Decisión (Camino 1 **FIRME**) |
|------|-----------------------------------|
| DDL | Ninguno. Sin ALTER. |
| API | Reusa T5 GET grilla `LIBRES`. Sin Resource nuevo. Sin POST en este acto. |
| Web | Habilitar ⇄ T5.4 + dialog Origin 1200×550 + swap in-memory. |
| Tests | Ampliar `turnos-agenda.spec.ts` |

Orden: Clarify **FIRME** 2026-09-14 → inventarios G0 → Gate UI dialog **antes** de template → Web → e2e → smoke G6.

## Cortes internos

| Gate | Exit |
|------|------|
| G0 | Clarify **FIRME** 2026-09-14 + inventarios **hecho** |
| G1 | N/A Flyway |
| G2 | N/A endpoint nuevo (reusa T5 GET) |
| G3 | Gate UI `$popupCambiarTurno` **antes** de habilitar icono / smoke — **done 2026-09-14** |
| G4 | Web icono → popup → rowSelect → tabla slots — **done 2026-09-14** |
| G5 | e2e — **PASS 2026-09-14** |
| G6 | Smoke ops Francisco — **PASS 2026-09-14** |

## Fuera

Ventana vecinos (WAIVE). Equipo D-TUR-17. T5.3-b (ya cobrado). Pre-agenda. T6 padre. T7.
