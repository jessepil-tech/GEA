---
title: Verify — T5.1c elegibilidad north + doc req
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros
---

# Verify — Elegibilidad north + doc req

**Gate:** **PASS / gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 **2026-09-08**. **No** cierra P-ORA-010.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Afiliado/doc + icono + spinner | **done** | `agenda.xhtml` L82 |
| Validar (seed) | **done** | D-TUR-42 · `elegibilidad_seed` |
| Filas doc req | **done** | GET doc-req · V49 |
| WS real OS | diferido | **P-ORA-010** |
| Pagar / saldo / coseguro | **done T5.1d** (chrome; montos `diferido(fixture)`) | [`turnos-agenda-cobros/`](../turnos-agenda-cobros/) |
| Popup obs paciente + CTA rechazo | **done T5.1d** | mismo |
| Info prestación | diferido | (fuera; sin slug nuevo si no se abre) |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Validar afiliado OK | sí | **e2e-migrado** | `turnos-agenda.spec.ts` |
| Validar rechazo | sí | **e2e-migrado** | mismo |
| Info convenio filas | sí | **e2e-migrado** | mismo |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ smoke stack real (G6). Universo de CU = esta matriz, no la batería E2E.

## Paridad UI (xhtml)

Inventarios G0: **done 2026-09-08**. Gate UI G3: fila afiliado `InputWid100` + icono misma fila (`agenda.xhtml` L82–115). Spinner modal L641.

## Smoke

**PASS** 2026-09-10 ops: paciente `20001` DNI `30111222` → icono `AUTORIZADO` sin popup; info convenio filas DIAGNOSTICO + OME. Seed `30999888` cobrado en T5.1d (popup + swap). Api live: `controlElegibilidad` 5001=`true` / 5100=`false`.

## Resultado

**PASS / gate-done T5.1c** 2026-09-10. P-ORA-010 **sigue abierto** (seed ≠ WS).
