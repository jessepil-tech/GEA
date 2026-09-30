---
title: Plan — persist obs + fecha prescripción
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.plan
---

# Plan

| Capa | Decisión |
|------|----------|
| DDL | Sin ALTER. V31 + V38. |
| API | Extender body otorgar + SELECT/UPDATE grilla. Validar required leyendo `servicio_centro` + `plan_convenio`. |
| Web | Cablear ngModel ya existente; calendar HIS; popup confirma. |
| Tests | Ampliar `turnos-agenda.spec.ts` |

Orden: **FIRME** → G0 inventarios → G2 API → Gate UI popup confirma G3 → Web G4 → e2e G5 → smoke G6.

## Cortes internos

| Gate | Exit |
|------|------|
| G0 | Clarify FIRME + inventarios |
| G1 | N/A Flyway |
| G2 | Otorgar escribe; grilla lee; toasts |
| G3 | Dialog confirma xhtml L287 **antes** de template |
| G4 | Web: persist + popup |
| G5 | e2e |
| G6 | Smoke ops Francisco — **PASS** 2026-09-10 |

## Fuera

Prep/req API; repetidos; Resource nuevo.
