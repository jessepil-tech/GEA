---
title: Tasks — T5.1c elegibilidad north + doc req
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros
---

# Tasks — Elegibilidad north + doc req

1. [x] **TSK-ops-g0-1** Abrir SDD + link T5.1b / relevamiento / backlog — **2026-09-08**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** Camino 1 en [spec.md](spec.md) — **2026-09-08**
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md) (borrador xhtml)
4. [x] **TSK-ops-g0-4** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md)
5. [x] **TSK-ops-g0-5** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md)
6. [x] **TSK-app-g1-0** Inventario DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md)
7. [x] **TSK-app-g1-1** Flyway `ts.doc_req_*` (tras FIRME)
8. [x] **TSK-app-g2-1** GET doc-req + validar elegibilidad seed
9. [x] **TSK-web-g3-0** Gate UI: fila afiliado+icono (xhtml **antes** template)
10. [x] **TSK-web-g4-1** North: máscara, enable, spinner, icono
11. [x] **TSK-web-g4-2** Cablear filas doc req al dialog info convenio
12. [x] **TSK-web-e2e** Ampliar `turnos-agenda.spec.ts`
13. [x] **TSK-ops-g6-1** Smoke stack real — **2026-09-10** (ops: 30111222 AUTORIZADO + filas doc req)
14. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-10**

## Gate UI (G3) — xhtml

| Path | Rol |
|------|-----|
| `…/asignacionTurnos/agenda.xhtml` L82–115, L641–644 | Fila afiliado/doc + icono + spinner |
| `…/asignacionTurnos/asignacionTurnos.xhtml` L446–500 | **Fuera** (pagar/saldo → cobros) |
| Dialog info convenio (T5.1b) | Consumidor GET doc-req |

Geometría DoD: afiliado `InputWid100` + icono **misma fila**; documento igual. Máscara plan. No `authPrimary`.

## Viaje Playwright (Clarify #9)

| Viaje | Decisión |
|-------|----------|
| Validar afiliado seed OK | e2e-migrado |
| Validar seed rechazo | e2e-migrado |
| Info convenio con filas doc req | e2e-migrado |
| Legacy HIS | no |
