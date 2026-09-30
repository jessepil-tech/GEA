---
title: Tasks — T5.1 hijo popups info north
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-popups
---

# Tasks — Info popups north

1. [x] **TSK-ops-g0-1** Abrir SDD + link T5.1/relevamiento — **2026-09-08**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** Camino 1 en [spec.md](spec.md) — **2026-09-08**
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md)
4. [x] **TSK-ops-g0-4** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md)
5. [x] **TSK-ops-g0-5** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md)
6. [x] **TSK-app-g1-1** Inventario DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md) · Flyway **N/A**
7. [x] **TSK-web-g3-0** Gate UI: botones info 32×32 misma fila lupa/plan (xhtml **antes** template) — **2026-09-08**
8. [x] **TSK-web-g4-1** Dialog info búsqueda + PNG HIS + botón north paciente — **2026-09-08**
9. [x] **TSK-web-g4-2** Dialog info convenio HIS (doc req chrome + obs) + highlight — **2026-09-08**
10. [x] **TSK-web-g4-3** Auto-popup obs — **WAIVE** D-TUR-39 (HIS Agenda no lo abre) — **2026-09-08**
11. [x] **TSK-web-g4-4** Persistir `descripcion` convenio en north al seleccionar — **2026-09-08**
12. [x] **TSK-web-e2e** Ampliar `turnos-agenda.spec.ts` (abrir ambos dialogs) — **2026-09-08**
13. [x] **TSK-ops-g6-1** Smoke stack real — **2026-09-10** (ops: info búsqueda PNG + info convenio chrome)
14. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-10**

## Gate UI (G3) — xhtml

| Path | Rol |
|------|-----|
| `…/asignacionTurnos/agenda.xhtml` L27–80, L522–589 | Fila paciente 4 botones; plan + info; dialogs |
| `…/asignacionTurnos/asignacionTurnos.xhtml` L150–177 | Auto obs convenio/plan 650px |

Geometría DoD: input paciente `InputWid100` + lupa + **info** + X **misma fila**; plan `InputWid100` + info **misma fila**. Dialogs: búsqueda ~978×618; convenio 550; auto-obs 650. No `authPrimary`.

## Viaje Playwright (Clarify #9)

| Viaje | Decisión |
|-------|----------|
| Abrir info búsqueda | e2e-migrado |
| Abrir info convenio | e2e-migrado |
| Legacy HIS | no |
