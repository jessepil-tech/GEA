---
title: Tasks — T5.1d cobros agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-cobros
---

# Tasks — Cobros agenda

1. [x] **TSK-ops-g0-1** Abrir SDD + link T5.1c / relevamiento / backlog — **2026-09-09**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** Camino 1 en [spec.md](spec.md) — **2026-09-09**
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md) (borrador xhtml)
4. [x] **TSK-ops-g0-4** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md)
5. [x] **TSK-ops-g0-5** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md)
6. [x] **TSK-app-g1-0** Inventario DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md)
7. [x] **TSK-app-g1-1** Resolver convenio default en `ts` — V50 `call_center.id_convenio_dflt` = 5100 / plan 51001
8. [x] **TSK-app-g2-1** API outcome rechazo + lecturas saldo/coseguro (saldo/coseguro display null hasta fixture caja)
9. [x] **TSK-web-g3-0** Gate UI: popups xhtml **antes** template — dialogs HIS `Información` / sin X
10. [x] **TSK-web-g4-1** Rechazo: swap north + popup obs
11. [x] **TSK-web-g4-2** Pagar / saldo / filas infoTurno (chrome; montos si hay valor)
12. [x] **TSK-web-e2e** Ampliar `turnos-agenda.spec.ts` — 12/12 PASS 2026-09-09 (rechazo popup + convenio default; autorizado sin popup)
13. [x] **TSK-ops-g6-1** Smoke stack real — **2026-09-10** (ops: 30999888 popup RECHAZADO + PARTICULAR HOSPITAL)
14. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-10**

## Gate UI (G3) — xhtml

| Path | Rol |
|------|-----|
| `…/asignacionTurnos/asignacionTurnos.xhtml` L446–460 | `$popUpInfoPagar` |
| mismo L462–475 | `$popUpSaldoCtaCtePaciente` |
| mismo L477–515 | `$popUpObservacionesPacientes` |
| `…/asignacionTurnos/infoTurno.xhtml` L97–123 | Saldo / coseguros |

Geometría DoD: modal `Información`, `closable=false`; Aceptar. Leyenda default roja. No `authPrimary` extra.

## Viaje Playwright (Clarify #9)

| Viaje | Decisión |
|-------|----------|
| Rechazo → popup + convenio default | e2e-migrado |
| Autorizado no abre popup | e2e-migrado |
| Pagar/coseguro | e2e-migrado **o** diferido(fixture) |
| Legacy HIS | no |
