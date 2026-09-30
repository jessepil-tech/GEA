---
title: Verify — prep / requisitos realización infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.verify
---

# Verify

**Gate:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1. D-TUR-49 · D-TUR-50.

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Prep HTML al abrir (1ª fila por edad) | **done** | GET `…/prep-req` |
| Tabla req realización prestación | **done** | JOIN `req_realizacion` |
| EmptyMessage sin filas | **done** | e2e empty |
| G1 DDL + seed CONS | **done** | V52 + V53 |
| Merge req equipo | **diferido** | D-TUR-17 |
| Concat prep repetidos | **done** | [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11 |
| Print prep | **N/A** | recepción / T7 |
| ABM maestros | **fuera** | seed ≠ ABM |
| Doc-req convenio | **reusa** | T5.1c |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | `/turnos/agenda` → abrir Asignar o Información un-turno prestación CONS → panel Preparación Previa con HTML seed → tabla Requisitos con fila seed |
| Viaje 2 | prestación sin maestros → emptyMessage `no_se_encontraron_registros` |
| Fixture | mocks `turnos-agenda.spec.ts` + seed Flyway CONS/`80001` |
| Legacy e2e | **no** |

E2E mocks ≠ G6. Universo de CU = esta matriz.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-10**. Gate UI = **rellenar** chrome T5.1e; no rehacer 1200×520. Print **N/A**.

## Smoke

**G6 stack real — PASS 2026-09-10** (ops Francisco, `/turnos/agenda`, «se ve bien»):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Asignar/Información CONS: Preparación Previa con HTML seed | **PASS** |
| 2 | Tabla Requisitos Realización con fila seed | **PASS** |

## Resultado

**PASS / gate-done** prep/req infoTurno 2026-09-10. Concat repetidos [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) **gate-done** 2026-09-11. T6 padre **diferido**. D-TUR-17 equipo.
