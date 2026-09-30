---
title: SDD hijo — Gate contexto Recepción
description: diferido de paridad-orientacion-web. Centro / recepción / box antes de operar.
version: 0.1.0
status: deferred
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.paridad-recepcion-gate
---

# Gate Recepción (`paridad-recepcion-gate`)

**Estado:** **diferido** — carpeta pagaré. Spec/plan **no abiertos** hasta cobrar.  
**Padre:** [`paridad-orientacion-web/`](../../plataforma/paridad-orientacion-web/) (índice
`/recepcion/inicio` sí va en el padre; **este** slug es el modal/paso
`inicioRecepcionCentro`).

## Por qué existe

Legacy pide **centro / recepción / box** antes de la cola. El padre de orientación no
implementa ese gate (Clarify #6) para no mezclar shell de menú con CU recepción.

## Cuando se abra

- Capa 3 recepción (hoy solo slices cola). Completar A2b gate o parcial fechado.
- Paridad: `inicioRecepcionCentro.xhtml` / beans de puesto.
- No WAIVE: Verona lo tiene.

## No es

Cola A/B (ya gate-done). T1 call center Turnos. Identity menús.
