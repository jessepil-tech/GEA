---
title: Verify — persist obs + fecha prescripción
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.verify
---

# Verify

**Gate:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1. D-TUR-47 · D-TUR-48.

| Capacidad | Estado | Nota |
|-----------|--------|------|
| Persist obs + fecha al otorgar | **done** | UPDATE `ts.turno` en `POST …/otorgar` |
| Lectura Información | **done** | GET grilla |
| Toasts `FECHA_PRESCRIPCION_*` | **done** | MessageBundle; futura también en Web |
| Popup confirma | **done** | xhtml L287; flag `confirmaPrescripcion` |
| Prep/req | **done** (hijo) | [`turnos-agenda-info-turno-prest/`](../turnos-agenda-info-turno-prest/) **gate-done** 2026-09-10 |
| Repetidos | **done** (hijo) | [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11 |
| Required centro/servicio e2e | **diferido(fixture)** | sin seed `req_fecha_prescrip_amb` |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Nota |
|-------|------------|----------|------|
| Asignar obs+fecha → Información | sí | **e2e-migrado** | mocks `turnos-agenda.spec.ts` |
| Fecha futura → toast | sí | **e2e-migrado** | |
| Popup confirma Aceptar | sí | **e2e-migrado** | fixture 400 → Aceptar |
| Required centro/servicio | sí | **diferido(fixture)** | no Oracle |
| Legacy HIS | — | **no** | |

E2E mocks ≠ G6. Universo de CU = esta matriz.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-10**. Gate UI popup confirma: `turnos-agenda-confirma-prescripcion-dialog` (header Confirmación, `closable=false`, Aceptar/Cancelar).

## Smoke

**G6 stack real — PASS 2026-09-10** (ops Francisco, `/turnos/agenda`):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Asignar con obs + fecha → Información las muestra | **PASS** |
| 2 | Cancelar Asignar no persiste | **PASS** |

## Resultado

**PASS / gate-done** persist infoTurno 2026-09-10. Prep/req [`turnos-agenda-info-turno-prest/`](../turnos-agenda-info-turno-prest/) **gate-done**. Repetidos [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11. T6 padre diferido.
