---
title: Tasks — cambiar horario turnos múltiples
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** — 2026-09-14 (Francisco «ok»)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-14 (xhtml + BB)
3. [x] **TSK-app-g1-1** DDL gaps N/A (sin Flyway)
4. [x] **TSK-web-g3-0** Gate UI `$popupCambiarTurno` **antes** de template — **2026-09-14**
5. [x] **TSK-web-g4-1** Habilitar ⇄ + dialog + swap in-memory (sin POST) — **2026-09-14**
6. [x] **TSK-web-e2e** Viaje reemplazo de hora + Cancelar — **2026-09-14** (`turnos-agenda.spec.ts`)
7. [x] **TSK-ops-g6-1** Smoke Francisco — **PASS 2026-09-14** («si funciona»)
8. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-14**

## Gate UI (G3) — xhtml

Paths: `Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/turnosMultiples.xhtml`

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| L301–303 | Icono slots `title=cambiar_horario` `fa-exchange` | col acciones **width="24"** |
| L540–575 | `$popupCambiarTurno` | **1200×550 cuerpo**; `closable=true`; header `Otros turnos disponibles`; tabla `scrollHeight="465"`; hora **52** / duración **20**; **sin** col prestación; footer solo Cancelar |

Inventarios copy/validaciones: **hecho G0** 2026-09-14.  
Look Origin: CSS `gt-his-cambiar-*` (T5.3-b). No se pedirá smoke hasta disposición+geometría OK o diferidos listados.

## Viaje Playwright (Clarify #8)

| Viaje | Decisión | Spec (G5) |
|-------|----------|-----------|
| Slot → ⇄ → popup → click LIBRE → fila nueva hora | e2e-migrado | `Hospital-Web/e2e/turnos-agenda.spec.ts` |
| Cancelar / X no cambia filas | e2e-migrado | mismo |
| Legacy HIS | no | sin fixture Oracle |
