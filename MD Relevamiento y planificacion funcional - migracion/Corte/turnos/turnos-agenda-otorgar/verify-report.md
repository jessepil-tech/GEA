---
title: Verify — T5 Turnos agenda / otorgar
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-otorgar
---

# Verify — T5

**Gate:** **PASS / gate-done** 2026-09-07 · Clarify **FIRME** 2026-09-07 · G0–G6 (e2e PASS; smoke stack real **PASS** ops 2026-09-08).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Ver grilla día + calendario | **done G2** | `AgendaGrillaEngine` · GET `/api/v1/turnos/agenda/grilla` · `/dias` |
| Reservar | **done G3** | `AgendaReservaEngine` · POST `…/reservar` |
| Tomar / lock sesión (opción A) | **done G3** | POST `…/tomar` · poll 59 s UI · D-TUR-28 |
| Expirar reservas 3/30 min | **done G3** | POST `…/expirar-reservas` (sin Quartz job v1) |
| Inhibición cruzada en reserva | diferido | `pp_inhibe` — sin `inhab_tur_*` PG |
| Otorgar + hist | **done G4** | `AgendaOtorgaEngine` · POST `…/otorgar` · `hist_turno` |
| Liberar + hist | **done G4** | POST `…/liberar` (sin unificación slots adyacentes v1) |
| Sobreturno | **done** T5 API + T5.2 UI | POST `…/sobreturno` · [`turnos-agenda-sobreturno/`](../turnos-agenda-sobreturno/) **gate-done** 2026-09-10 |
| UI agenda HIS | **done G5** | `/turnos/agenda` · `turnos-agenda.component.ts` |
| Equipo | diferido | D-TUR-17 |
| Repetidos | **done** T5.3 | [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11 |
| Múltiples | diferido | `turnos-agenda-multiples` |
| Pre-agenda | **done T5.6** | [`turnos-agenda-preagenda/`](../turnos-agenda-preagenda/) **gate-done** 2026-09-15 |
| Ficha west paciente | **done T5.1** | [`turnos-agenda-ficha-paciente/`](../turnos-agenda-ficha-paciente/) **gate-done** 2026-09-08 |
| Elegibilidad / cobros | **done T5.1c/d** (WS real P-ORA-010) | [`turnos-agenda-elegibilidad-cobros/`](../turnos-agenda-elegibilidad-cobros/) · [`turnos-agenda-cobros/`](../turnos-agenda-cobros/) |
| ABM tope paciente | diferido | `turnos-ctrl-ctd-max-pac` · `ctrl_turnos_pac` |
| Reasignar | **done T6.1** | D-TUR-26 → [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) **gate-done** 2026-09-10 |
| Consulta Agenda Turnero | **done T5.5** | [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) **gate-done** 2026-09-15 |
| Consultas menú | diferido | [`turnos-consultas-operador/`](../turnos-consultas-operador/) |
| Imprimir turno BIRT / SMS | diferido T7 | — |
| Puente HOS-APP `inicioAgenda` | **WAIVE** | D-TUR-22 — otra app (`loginByPass`), no HIS |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Consultar grilla día | sí | **e2e-migrado** | `Hospital-Web/e2e/turnos-agenda.spec.ts` · **PASS** 2026-09-07 |
| Reservar + otorgar | sí | **e2e-migrado** | Mismo spec · **PASS** 2026-09-07 |
| Liberar | sí | **e2e-migrado** | Mismo spec · **PASS** 2026-09-07 |
| Legacy HIS | — | **no** | Sin fixture Oracle vigente |

Fixture e2e (mocks CI): centro `1001` · servicio `10` · personal `90001` · convenio `5001` · plan `1` · paciente `20001` · prestación `CONS`/`80001` · call center `92001` · turno `40001` · fecha `2026-09-14` · motivo liberación `91004` — ver `e2e/helpers/turnos-agenda-fixtures.ts`.  
E2E mocks CI ≠ smoke stack real (G6).

## Paridad UI (xhtml)

Fuente: `agenda.xhtml` · `asignacionTurnos.xhtml` · `infoTurno.xhtml`.  
Inventarios G0: [`inventario-copy-msg.md`](inventario-copy-msg.md) · [`inventario-validaciones.md`](inventario-validaciones.md) — **done 2026-09-07**.

| Control | Estado | Nota |
|---------|--------|------|
| Inventario msg.* ↔ labels.ts | **done G5** | `turnos-agenda-labels.ts` |
| Inventario interacción UI | **done G5** (corr. 2026-09-07) | [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md) · `turnos-agenda-row-menu.component.ts` |
| Inventario validaciones | **done G5** | `turnos-agenda-validation.ts` — elegibilidad/cobros diferido |
| Disposición vs xhtml | **done G5** (corr. 2026-09-07) | north 4 cols · west 260px · grilla + **menú gear 24px** · leyenda 6 estilos |
| Buscadores prest/prof | **done G5** | `PrestacionBuscadorDialog` · `HabBuscadorDialog` |
| Buscador paciente/convenio | **done T5.1** | [`turnos-agenda-ficha-paciente/`](../turnos-agenda-ficha-paciente/) |
| Modal libera (no window.confirm) | **done G5** | `turnos-liberar-dialog.component.ts` |
| Dialog infoTurno + poll 59 s | **done G5** | `turnos-info-turno-dialog.component.ts` |
| Toast CRUD | **done G5** | `notifyHorarios*` |
| Dark / breadcrumb menú | **done G5** | TURNOS → Turnero / Agenda habilitado |
| Equipo disabled | **done** (diferido funcional) | D-TUR-17 |
| Gaps DDL | **done G1** | V46 motivo `LIBERACION_TURNO` · `ctrl_turnos_pac` diferido |

## Smoke

**Gate v1.10 disposición:** cerrada (north + west + grilla + dialogs).

**G6 stack real — PASS 2026-09-08** (smoke manual ops, misma pasada T5.1):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Identity `:8080` · Api `:8081` · Web `:4200` · Reports N/A T5 | **PASS** |
| 2 | `/turnos/inicio` → call center `92001` en sessionStorage | **PASS** |
| 3 | `/turnos/agenda` → filtros demo → Consultar grilla `2026-09-14` | **PASS** |
| 4 | Menú gear → Asignar Turno → dialog info → Otorgar | **PASS** |
| 5 | Menú gear → Liberar → motivo `91004` | **PASS** |

Convivencia seed AGI: usar fecha/rango dedicado (como T4) — no mezclar con OTORGADO legacy sin coordinar.

## Resultado

**PASS / gate-done T5** con diferidos explícitos (equipo, repetidos/múltiples/pre-agenda, elegibilidad, inhibe JDBC, merge slots liberar, tope sobreturno, T6–T7). Ficha/buscadores north+west cobrados en T5.1.  
E2e Playwright **PASS** 2026-09-07. Smoke stack real **PASS** 2026-09-08.  
Siguiente corte Turnos: T6 padre diferido (`turnos-ciclo-vida`). Prep/req infoTurno **gate-done**.
