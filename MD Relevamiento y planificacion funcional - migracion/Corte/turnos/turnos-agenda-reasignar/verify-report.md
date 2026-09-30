---
title: Verify — T6.1 reasignar agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-reasignar
---

# Verify — Reasignar agenda

**Gate:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 **2026-09-09**. **No** cierra T5 (ya gate-done) ni T6 padre.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Menú REASIGNAR → sesión + north + toast + overlay | **done** | `agenda.xhtml` L279 · `BBAgenda` ~2420 |
| CANCELAR_REASIGNACION limpia sesión | **done** | xhtml L282 · ~2440 |
| Overlay `PENDIENTE_LIBERAR` (UI only) | **done** | `BBAgenda` ~861 |
| Popup `$popupObservacionesReasignarTurno` | **done** | xhtml L122 · otorga ~1112 |
| Cierre otorga + libera origen | **done** | `otorgarTurnoPac(turno, turnoAReasignar)` |
| Cola `turnosAReasignar.xhtml` | **diferido** `turnos-ciclo-vida` | T4 / A8 |
| Suspender grilla | **diferido** `turnos-ciclo-vida` | `suspenderGrillaTurnos` |
| Reemplazo profesional | **diferido** `turnos-ciclo-vida` | `reemplazoPersonalGrillaTurnos` |
| Job vencidos / hist pantalla | **diferido** `turnos-ciclo-vida` | A8 |
| Mail/SMS/BIRT | **diferido** T7 | — |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Reasignar → origen pendiente + north paciente | sí | **e2e-migrado** | `turnos-agenda.spec.ts` T6.1 |
| Cancelar limpia sesión | sí | **e2e-migrado** | mismo |
| Asignar slot nuevo libera origen | sí | **e2e-migrado** | mismo |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ smoke stack real (G6). Universo de CU = esta matriz, no la batería E2E.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-09**. Gate UI G3: **hecho** (menú gear L279 + popup 650 L122).

## Smoke

**G6 stack real — PASS 2026-09-10** (ops, `/turnos/agenda`):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Identity `:8080` · Api `:8081` · Web `:4200` | **PASS** |
| 2 | Gear OTORGADO → REASIGNAR: toast, overlay `PENDIENTE_LIBERAR`, north paciente; origen sigue OTORGADO en BD | **PASS** |
| 3 | CANCELAR REASIGNACIÓN: limpia overlay; no write | **PASS** |
| 4 | REASIGNAR → Asignar LIBRE → popup Observaciones → Aceptar: nuevo OTORGADO, origen LIBRE | **PASS** |
| 5 | Historial: fila origen **REASIGNADO**; destino **OTORGADO** (`pf_hist_turno` flag `S`) | **verificado** — Francisco 2026-09-18 «si los 2 los probe» (re-probe post-fix; filas 16:39 OTORGADO no cuentan) |

Nota: primer otorgar falló por `commit`/`rollback` JDBC local enlisted en JTA (`JdbcTurnosAgendaAdapter.otorgar`). Fix: tx solo `TransactionMiddleware`. Re-smoke **PASS**.

E2E mocks **16/16** `turnos-agenda.spec.ts` (incl. 3 viajes T6.1) ≠ G6.

## Resultado

**PASS / gate-done T6.1** 2026-09-10. T6 padre `turnos-ciclo-vida` y T7 siguen diferidos. T5.1b/c/d **gate-done** 2026-09-10.
