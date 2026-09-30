---
title: Verify — T5.1 hijo popups info north
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-popups
---

# Verify — Info popups north

**Gate:** **PASS / gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 **2026-09-08**. **No** cierra T5 (ya gate-done).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Info búsqueda paciente | **done** | `popupInfoBusqueda` · PNG |
| Info convenio (botón plan) | **done** | `popupInfoConvenio` chrome HIS |
| Auto obs convenio/plan | **WAIVE** | D-TUR-39 — HIS Agenda no dispara `showObservacionesConvenio` |
| Highlight botón si hay obs | **done** | `ui-state-error` |
| Filas doc requerida | **done T5.1c** | [`turnos-agenda-elegibilidad-cobros/`](../turnos-agenda-elegibilidad-cobros/) |
| PNG InfoBusquedaPac | **done** | `public/images/turnos/` |
| Elegibilidad seed / icono | **done T5.1c** | mismo |
| WS real OS | diferido | **P-ORA-010** |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Abrir info búsqueda | sí | **e2e-migrado** | `turnos-agenda.spec.ts` |
| Abrir info convenio | sí | **e2e-migrado** | mismo |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ smoke stack real (G6). Universo de CU = esta matriz, no la batería E2E.

## Paridad UI (xhtml)

Inventarios G0: **done 2026-09-08**. Gate UI G3 + Web G4 **done 2026-09-08**.

## Smoke

**PASS** 2026-09-10 ops: `/turnos/agenda` · Identity `:8080` · Api `:8081` · Web `:4200` · info búsqueda PNG · info convenio chrome (filas doc req cobradas en T5.1c).

## Resultado

**PASS / gate-done T5.1b** 2026-09-10. P-ORA-010 sigue abierto. Auto-popup obs **WAIVE** D-TUR-39.
