---
title: SDD — Ciclo de vida llamado anunciador
description: Paridad LLAMAR S→N, limpieza/caducidad y push WS tras transiciones.
version: 0.3.0
status: gate-done
owner: grupogea
last_updated: 2026-08-26
phase_id: sdd.hospital.ciclo-vida-llamado-anunciador
---

# Ciclo de vida — `ts.llamado_anunciador` (display anunciador)

**Estado:** **gate-done** (C0–C3 completos) · enganche anulación/atención = **Fase 4**  

Padres: [`cu-clinico-b1-llamar-recepcion/`](../../recepcion/cu-clinico-b1-llamar-recepcion/),
[`paridad-recepcion-cola/`](../../recepcion/paridad-recepcion-cola/) M2,
[`piloto-agi-anunciador/`](../piloto-agi-anunciador/),
[`anunciador-ws/`](../anunciador-ws/).  
**Cierre perímetro:** [`cierre-paridad-agi-anunciador/`](../cierre-paridad-agi-anunciador/) (P0 = este SDD).

Hijo escritor clínico P1: [`cu-llamar-atencion-medica/`](../../recepcion/cu-llamar-atencion-medica/)
**gate-done** 2026-09-16 (Llamar UI). Enganche `quitar*` / cancelar atención = Fase 4 de este ciclo.

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | RF / CA / Clarify **FIRME** |
| [plan.md](plan.md) | Cortes C0–C3 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate + checklist smoke C3.2b |

## Clarify (resumen)

- `S→N` al **consumir en lectura** de la TV (`GET …/llamados` / WS onOpen).
- Ventana `ctd_minutos_muestra_pac` (default 5) + safety batch 1h.
- DELETE re-call + puerto `quitar*` (C2.1); enganche CUs → Fase 4.
- Tras consume `S→N`, **push WS** a otras TVs (RF-3).

## Implementado (Hospital-Api)

- Consume-on-read · peek tras insert · re-call · expire 1h · quitar · WS re-push
- IT `llamados_consumeOnRead_*`, `quitarPorPacienteServicio_*`, `quitarPorColaEsperaRecep_*`
- Docs padres/matriz alineados (C3.2a)

> **2026-08-25:** cutover `ts` · **2026-08-26:** C2.1 + WS re-push + C3.2a docs.
