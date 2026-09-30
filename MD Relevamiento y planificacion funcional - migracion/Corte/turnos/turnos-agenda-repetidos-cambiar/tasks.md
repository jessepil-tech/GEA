---
title: Tasks — cambiar horario turnos repetidos
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** — 2026-09-11 (Francisco «ok firme»)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-11
3. [x] **TSK-app-g1-1** DDL gaps N/A (sin Flyway)
4. [x] **TSK-web-g3-0** Gate UI `$popupCambiarTurno` **antes** de template — **2026-09-11**
5. [x] **TSK-web-g4-1** Habilitar ⇄ + dialog + use-case reserva (+ libera anterior) — **2026-09-11**
6. [x] **TSK-web-e2e** Viaje reemplazo desde reservado + obs + Cancelar — **2026-09-11** (`turnos-agenda.spec.ts`)
7. [x] **TSK-ops-g6-1** Smoke Francisco — **PASS 2026-09-11** («joya se ve bien»)
8. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-11**

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `…/asignacionTurnos/turnosRepetidos.xhtml` L161–163 | Icono reservados `title=cambiar_horario` `ui-icon-transferthick-e-w` | col acciones **width="50"** |
| L180–182 | Icono observaciones | col **width="60"** |
| L311–382 | `$popupCambiarTurno` | **1200×550 cuerpo**; `closable=true`; header `Otros turnos disponibles`; tabla `scrollHeight="461"`; hora/duración **60**; footer solo Cancelar |

Inventarios copy/validaciones: **hecho G0** 2026-09-11.  
No se pedirá smoke hasta disposición+geometría OK o diferidos listados.

## Viaje Playwright (Clarify #8)

| Viaje | Decisión | Spec (G5) |
|-------|----------|-----------|
| Reservado → popup → click LIBRE → fila nueva hora | e2e-migrado | `Hospital-Web/e2e/turnos-agenda.spec.ts` |
| Obs → popup → click LIBRE → pasa a reservados | e2e-migrado (si fixture parcial) / `diferido(fixture)` | mismo |
| Legacy HIS | no | sin fixture Oracle |
