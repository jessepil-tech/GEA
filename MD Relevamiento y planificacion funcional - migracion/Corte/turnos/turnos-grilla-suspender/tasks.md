---
title: Tasks — T6.4 · suspender / quitar suspensión de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 (Francisco 2026-09-18)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify
3. [x] **TSK-ops-g0-3** COUNT `ts.turno` rango G6 (acto T4, no seed) — 3 filas 2026-09-18 centro 1001 serv 10
4. [x] **TSK-ops-g0-4** Gate UI: xhtml + inventarios medidos **antes** de template (popup 400×60, closable=false)
5. [x] **TSK-app-g2-1** GET lista candidatos JDBC (serv/pers; equipo ignorado)
6. [x] **TSK-app-g2-2** POST suspender (ids + motivo + horas) port `f_suspender_turnos`
7. [x] **TSK-app-g2-3** POST quitar port `f_quitar_suspension`
8. [x] **TSK-app-g2-4** Golden + rollback (`TransactionMiddlewareTest`) + dos actores (engine, sin FOR UPDATE) — 2026-09-18
9. [x] **TSK-web-g4-1** Página suspender + menú
10. [x] **TSK-web-g4-2** Página quitar suspensión + menú
11. [x] **TSK-web-g5-1** e2e-migrado 4 viajes (stub CI; ≠ G6)
12. [x] **TSK-ops-g6-1** Smoke Francisco (LIBRE → SUSPENDIDO → LIBRE, id 17255154)
13. [x] **TSK-ops-g6-2** Verify PASS + gobierno — 2026-09-18 (`verificar-sdd.sh turnos-grilla-suspender`)

## Gate UI

| Path | Rol | DoD |
|------|-----|-----|
| `suspenderGrillaTurnos.xhtml` | Pantalla | Radio T4, north, tabla selección, popup motivo, south |
| `quitarCancelacionGrillaTurnos.xhtml` | Pantalla | Misma familia; sin popup motivo |
| `hospital-menu.catalog.ts` folder Agenda turnos | Menú | Dos leafs junto a Generar/Eliminar/Consulta |

**Prohibido:** fork `/turnos/agenda`; T6 padre entero; seed de `turno`; habilitar equipo; portar mail/SMS/WA; reemplazo; vencidos; clonar BODY GENERAL.
