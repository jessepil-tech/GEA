---
title: Tasks — T6.1 reasignar agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-reasignar
---

# Tasks — Reasignar agenda

1. [x] **TSK-ops-g0-1** Abrir SDD + link T5 D-TUR-26 / A8 / relevamiento / backlog — **2026-09-09**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** Camino 1 en [spec.md](spec.md) — **2026-09-09**
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md)
4. [x] **TSK-ops-g0-4** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md)
5. [x] **TSK-ops-g0-5** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md)
6. [x] **TSK-app-g1-0** Inventario DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md)
7. [x] **TSK-app-g1-1** Confirmar G1: **no** Flyway estado `PENDIENTE_LIBERAR` — **2026-09-09**
8. [x] **TSK-app-g2-1** API: otorga T5 con `idTurnoOrigen` opcional (libera origen) — **2026-09-09**
9. [x] **TSK-web-g3-0** Gate UI: xhtml menú + popup **antes** template — **2026-09-09**
10. [x] **TSK-web-g4-1** Web: sesión + north paciente + toast + overlay — **2026-09-09**
11. [x] **TSK-web-g4-2** Web: cancelar limpia; popup obs; cierre otorga+libera — **2026-09-09**
12. [x] **TSK-web-e2e** Ampliar `turnos-agenda.spec.ts` (3 viajes Camino 1) — **2026-09-09**
13. [x] **TSK-ops-g6-1** Smoke stack real — **2026-09-10** (ops: reasignar / cancelar / cierre otorga+libera; fix JTA `otorgar` sin commit JDBC local)
14. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-10**

## Gate UI (G3) — xhtml

| Path | Rol |
|------|-----|
| `…/asignacionTurnos/agenda.xhtml` L279–284 | Menú `REASIGNAR` / `CANCELAR_REASIGNACION` (rendered según overlay) |
| `…/asignacionTurnos/asignacionTurnos.xhtml` L122–147 | `$popupObservacionesReasignarTurno` |

Geometría DoD: menú gear existente (no botón inline). Overlay origen (pintura fila, no chip suelto). Popup header `Observaciones`, `closable=false`, width **650**, textarea `InputWid100`, Aceptar/Cancelar `InputWid100` misma fila. No `authPrimary` extra.

**Prohibido** pedir smoke sin paridad xhtml (salvo diferido listado).

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Reasignar → origen pendiente + north paciente | e2e-migrado |
| Cancelar limpia sesión | e2e-migrado |
| Asignar slot nuevo libera origen | e2e-migrado |
| Legacy HIS | no |
