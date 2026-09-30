---
title: Verify — T5.2 sobreturno agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno
---

# Verify — Sobreturno agenda

**Gate:** **gate-done** 2026-09-10 · Clarify **FIRME Camino 2** **2026-09-10**. **No** cierra T5 (ya gate-done) ni T7.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| API `POST …/sobreturno` | **done T5** | `ReservarSobreturnoCommand` |
| Accordion 170px chrome | **done** | `turnos-agenda-shell-menu` |
| Disparador Turnero Sobreturno | **done** | xhtml L72 |
| Popup `$popupSobreturno` | **done** | `turnos-sobreturno-dialog` |
| Aceptar → reserva + otorga | **done** | POST T5 + infoTurno T5.1e |
| Popup turnos de hoy | **done** | xhtml L600 |
| Volver south accordion | **fuera** (producto) | breadcrumb |
| Liberar sobreturno DELETE | **done T5** | — |
| Equipo usable | **done** | [`turnos-agenda-sobreturno-equipo`](../turnos-agenda-sobreturno-equipo/) 2026-09-28 |
| Tope SOBRETURNOS | diferido | `turnos-ctrl-ctd-max-pac` |
| ABM Paciente | diferido (chrome disabled) | `turnos-asignacion-shell` |
| Otras hojas Turnero | diferido (chrome disabled) | slugs spec RF-7 |
| Mail/imprimir | diferido | T7 |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Sin paciente → toast | sí | **e2e-migrado** | `turnos-agenda.spec.ts` |
| Abre popup | sí | **e2e-migrado** | mismo |
| Otorga sobreturno | sí | **e2e-migrado** | mismo |
| Turnos de hoy | sí | **e2e-migrado** | fixture OTORGADO |
| Accordion chrome | sí | **e2e-migrado** | mismo |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ smoke stack real (G6). Universo de CU = esta matriz, no la batería E2E.

## Paridad UI (xhtml)

Inventarios G0: **Camino 2 2026-09-10**. Gate UI G3: accordion 170 + popup L316. G4/G5 **hecho**.

## Smoke

**G6 stack real — PASS 2026-09-10** (ops Francisco, `/turnos/agenda`):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Accordion Turnero → Sobreturno → Aceptar → INSERT `RESERVADO` `sobreturno='S'` | **PASS** |
| 2 | infoTurno modo otorga (mismo chrome T5.1e) | **PASS** |

Nota: primer INSERT falló por VALUES desalinhados (`econsulta character(1)`). Fix adapter; re-smoke **PASS**.

## Resultado

**PASS / gate-done T5.2** 2026-09-10. Equipo en [`turnos-agenda-sobreturno-equipo`](../turnos-agenda-sobreturno-equipo/). Tope SOBRETURNOS, T7 y T6 padre siguen diferidos.
