---
title: Tasks — turnos repetidos agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** — 2026-09-10 (Francisco «ok firme»)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-10
3. [x] **TSK-app-g1-1** Sin Flyway tmp: observaciones en DTO (inventario DDL)
4. [x] **TSK-app-g2-1** POST reserva repetidos (CQRS, JDBC `ts`, no PL/pgSQL)
5. [x] **TSK-app-g2-2** Mensajes 0 encontrados / parcial
6. [x] **TSK-web-g3-0** Gate UI popup + página HIS **antes** de template
7. [x] **TSK-web-g4-1** Menú + popup + tablas + Volver/Asignar Turnos + infoTurno tabla
8. [x] **TSK-web-e2e** Viajes reserva 2 + empty
9. [x] **TSK-ops-g6-1** Smoke Francisco — **PASS** 2026-09-11 («el corte de repetidos lo veo bien»)
10. [x] **TSK-ops-g6-2** Verify PASS + gobierno — 2026-09-11

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `agenda.xhtml` L285 | Menú `TURNOS REPETIDOS` | mismo gear; icon refresh |
| `turnosRepetidos.xhtml` L205–296 | `$popupTurnosRepetidos` | header Turnos Repetidos; `closable=false`; modal; días en una fila; ctd; botones 105px |
| L40–198 | Página north ficha + center 2 tablas + south 110px | `InputWid100`; emptyMessage |
| `infoTurno.xhtml` L166–213 | Tabla turnos reservados | scroll; calendar fecha + obs por fila |
| L311 | Cambiar horario 1200×550 | **done** [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/) **gate-done** 2026-09-11 |

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Menú → reservar 2 → Asignar Turnos → otorga | e2e-migrado |
| 0 encontrados → popup mensaje | e2e-migrado |
| Legacy HIS | no |
