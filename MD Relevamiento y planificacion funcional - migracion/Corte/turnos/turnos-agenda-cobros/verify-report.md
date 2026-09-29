---
title: Verify — T5.1d cobros agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-cobros
---

# Verify — Cobros agenda

**Gate:** **PASS / gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 **2026-09-09**. **No** cierra P-ORA-010 ni facturación.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Rechazo → convenio dflt + afiliado vacío | **done** | `BBAgenda` ~2075 · V50 |
| Popup `$popUpObservacionesPacientes` | **done** | xhtml L477 |
| `popUpInfoPagar` | done chrome · montos **diferido(fixture)** | xhtml L446 |
| `popUpSaldoCtaCtePaciente` | done chrome · montos **diferido(fixture)** | xhtml L462 |
| Coseguro/saldo en infoTurno | done chrome · montos **diferido(fixture)** | `infoTurno.xhtml` L97 |
| Cálculo tarifario / caja | diferido | facturación |
| WS real OS | diferido | **P-ORA-010** |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Rechazo → popup + swap convenio | sí | **e2e-migrado** | `turnos-agenda.spec.ts` |
| Autorizado sin popup | sí | **e2e-migrado** | mismo |
| Pagar/coseguro | sí | **diferido(fixture)** | sin seed caja/tarifario en `ts.turno` |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ smoke stack real (G6). Universo de CU = esta matriz, no la batería E2E.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-09**. Gate UI G3: dialogs HIS (header Información, sin X, Aceptar).

## Smoke

**PASS** 2026-09-10 ops: DNI `30999888` (PEREZ LUIS / RODRIGUEZ ANA) → popup Información `RECHAZADO` + leyenda convenio default; north afiliado vacío y convenio **PARTICULAR HOSPITAL**. Autorizado `30111222` no abre popup. Montos pagar/saldo **no** humedos (`diferido(fixture)`).

## Resultado

**PASS / gate-done T5.1d** 2026-09-10. P-ORA-010 y caja/tarifario siguen diferidos.
