---
title: Spec — E2E mostrador
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.e2e-mostrador
---

# Spec — E2E mostrador

## Problema

Los slices del circuito ambulatorio de mostrador están **gate-done por separado**.
El criterio de avance exige un viaje de punta a punta con filas **nacidas en `ts`**
(fuente #1), no seed ni dump Oracle. Hoy el e2e de agenda en Web usa **mocks**;
no hay spec de cola/AGI/TV.

## Capacidades (viaje, no pantallas nuevas)

| Capacidad | Evidencia legacy | ¿Escritura? | ¿Realtime? | Después |
|-----------|------------------|-------------|------------|---------|
| Ofertar `LIBRE` y otorgar | T4/T5 ya migrados | `ts.turno` | no | AGI lista OTORGADO |
| Identificar / recepcionar en AGI o cola | G1 / cola M1–M4 | cola / recepción | no | Llamar |
| Llamar → TV | CU-B.1 + ciclo + WS | `llamado_anunciador` | WS display | ticket si está en el tramo |
| Purga cola 6 h / apaga TV 1 h / vigencia hab | jobs | sí (prod) | no | **fuera** de este viaje diurno |

## Clarify

1. **Pipeline config** — T2/T3/T4 cubiertos por slugs Turnos. Padres FK = bootstrap #2
   o ya en PG. Gate centro/puesto = `diferido(paridad-recepcion-gate)`.
2. **Happy path** — login staff → otorgar un `LIBRE` a paciente con id vigente →
   el mismo turno aparece en AGI/recepción → entra a cola → Llamar → el display
   muestra el llamado. Todo contra `hospital_api`, no DevServices ni mocks.
3. **Ciclo de vida** — el `OTORGADO` no se borra al terminar el viaje (es evidencia).
   Jobs de vencido/purga = `diferido(relevamiento-procesos-programados)` (premisa:
   prod encendido; HOSPROD apagado por no ser prod).
4. **Errores / acceso** — actor staff con rol que ya usan los ITs; 401 sin Bearer
   ya cubierto en slices. Fail-open de menú **no** es este corte.
5. **Side-effects** — TV/WS; ticket del tramo G1-D si está cobrado; si no,
   `diferido` al corte de ticket AGI (plan F3).
6. **Fuera** — T6 · Cola B acciones menú M5 · C5 ABM · VALIDADORES P-ORA-010 (seed de
   elegibilidad si el otorgar lo exige; no WS real) · A–C módulo Recepción.
   Llamar/TV del autorizado = `cola-b-llamar` Clarify **B** (API; sin botón
   espera-amb).
7. **Paridad UI / Gate arranque** — **N/A** (no hay xhtml nuevo). Geometría ya
   cobrada en los slugs de cada pantalla.
8. **Viaje Playwright** — **e2e-migrado** (este corte *es* el viaje). Spec nuevo
   en `Hospital-Web/e2e/` contra API real. Mocks de `turnos-agenda.spec.ts` **no**
   cierran. Legacy HIS = N/A (sin fixture Oracle de este viaje).

## RF de escritura

Crear `OTORGADO` (T5) → visible en AGI/cola → transición Llamar → fin en TV.
Ledger: `id` de `ts.turno` + query; id de cola/llamado si el CU los persiste.

## Relevamiento

[`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) ·
[`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/) ·
[`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).
