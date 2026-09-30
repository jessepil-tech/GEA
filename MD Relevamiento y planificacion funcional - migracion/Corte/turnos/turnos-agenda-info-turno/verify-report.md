---
title: Verify — T5.1e chrome infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno.verify
---

# Verify

**Gate:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1. Acto otorga/cancelar **reusa T5**.

| Capacidad | Estado | Nota |
|-----------|--------|------|
| Chrome paneles un-turno 1200×520 | **done** | `infoTurno.xhtml`; titlebar fuera del 520 |
| Acto otorga/cancelar/poll | **reusa T5** | testids iguales |
| Ficha + doc-req al abrir | **done** | GET ficha + GET doc-req |
| Repetidos | **done** (hijo) | [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11 |
| Múltiples | **diferido** | `turnos-agenda-multiples` |
| Persist obs/prescripción | **done** (hijo) | [`turnos-agenda-info-turno-persist/`](../turnos-agenda-info-turno-persist/) **gate-done** 2026-09-10 |
| Prep/req filas | **done** (hijo) | [`turnos-agenda-info-turno-prest/`](../turnos-agenda-info-turno-prest/) **gate-done** 2026-09-10 |
| Print prep | **N/A** | solo recepción HIS |
| Tipo paciente | **N/A** | sin columna ficha; input vacío |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto? | sí |
| Decisión | **e2e-migrado** |
| Viaje | abrir infoTurno → Datos del Paciente + Datos Turno o Sobreturno |
| Fixture | mocks `turnos-agenda.spec.ts` |
| Legacy e2e | no |

E2E mocks CI ≠ smoke stack real (G6). Universo de CU = esta matriz, no la batería E2E.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-10**. Gate UI: dialog cuerpo 520; `InputWid100` = celda; tabla Datos Turno; rowspan obs+prep; disabled `#dadada`. Canon v1.13.

## Smoke

**G6 stack real — PASS 2026-09-10** (ops Francisco, `/turnos/agenda`):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Información / Asignar: chrome HIS sin scroll interno | **PASS** |
| 2 | Datos Turno/Sobreturno alineados; inputs disabled gris Verona | **PASS** |
| 3 | Ficha (sexo/fecha nac) + documentación requerida al abrir | **PASS** |

## Resultado

**PASS / gate-done T5.1e** 2026-09-10. Persist y prep/req **gate-done**. Repetidos [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11. Cambiar horario [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/) **gate-done** 2026-09-11. T6 padre diferido.
