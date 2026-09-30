---
title: Tasks — persist obs + fecha prescripción
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** (Francisco «ok firme» 2026-09-10)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-10
3. [x] **TSK-app-g2-1** API: body otorgar + UPDATE obs/fecha + SELECT grilla
4. [x] **TSK-app-g2-2** Validaciones MessageBundle + flags centro/plan
5. [x] **TSK-web-g3-0** Gate UI `$popUpConfirmaTurnoPrescripcion` **antes** template
6. [x] **TSK-web-g4-1** Web: POST body + Información muestra valores
7. [x] **TSK-web-e2e** Viajes persist + toast futura + popup confirma
8. [x] **TSK-ops-g6-1** Smoke Francisco — **PASS** 2026-09-10 («ok»)
9. [x] **TSK-ops-g6-2** Verify PASS + gobierno

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `infoTurno.xhtml` L125–148 | Calendar fecha + textarea obs (ya T5.1e) | Editable solo otorga; disabled `#dadada` |
| `asignacionTurnos.xhtml` L287–305 | `$popUpConfirmaTurnoPrescripcion` | header `Confirmación`; `closable=false`; modal; copy vencida/sin fecha; `¿Desea continuar?`; Aceptar/Cancelar; **no** `authPrimary` |

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Asignar obs+fecha → Información las muestra | e2e-migrado |
| Fecha futura → toast | e2e-migrado |
| Popup confirma Aceptar | e2e-migrado |
| Required si no hay fixture flag | diferido(fixture) |
| Legacy HIS | no |
