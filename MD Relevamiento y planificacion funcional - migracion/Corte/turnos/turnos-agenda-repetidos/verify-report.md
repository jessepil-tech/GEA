---
title: Verify — turnos repetidos agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos.verify
---

# Verify

**Gate:** **gate-done** 2026-09-11 · Clarify **FIRME** Camino 1. D-TUR-51 · D-TUR-52.

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Menú + popup + reserva N | **done** | spec RF-1–3 |
| Tablas + mensajes 0/parcial | **done** | |
| Volver libera | **done** | |
| Asignar Turnos + infoTurno tabla | **done** | |
| Concat prep/req | **done** | reusa T5.1e-q |
| Calendario cabecera + selected con foco | **done** | regla UI v1.14 |
| Checks días (init horario; independientes) | **done** | `f_get_dias_atencion` |
| Cambiar horario | **done** T5.3-b | [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/) **gate-done** 2026-09-11 |
| Múltiples / pre-agenda | **diferido** | slugs T5 |
| Equipo | **diferido** | D-TUR-17 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | gear TURNOS REPETIDOS → días+ctd → reservar → tabla → Asignar Turnos → otorga |
| Viaje 2 | 0 slots → popup `No se encontraron turnos…` |
| Fixture | mocks `turnos-agenda.spec.ts` |
| Legacy e2e | **no** |

E2E mocks ≠ G6. Universo de CU = esta matriz.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-10**. Gate UI popup+página **antes** de smoke. Ajustes visuales G6 (cabecera D L M M J V S, selected `:focus`, checks HIS) **PASS** ops 2026-09-11.

## Smoke

**G6 stack real — PASS 2026-09-11** (ops Francisco, `/turnos/agenda`, «el corte de repetidos lo veo bien»):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Gear TURNOS REPETIDOS → popup días/ctd → reserva N → tablas / observaciones HIS | **PASS** |
| 2 | Visual: calendario thead + selected con foco; checks init e independientes | **PASS** |

## Resultado

**PASS / gate-done T5.3** 2026-09-11. Hijo cambiar horario [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/) **gate-done** 2026-09-11. T6 padre **diferido**. D-TUR-17 equipo.
