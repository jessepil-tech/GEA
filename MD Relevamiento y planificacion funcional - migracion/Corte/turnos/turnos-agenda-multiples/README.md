---
title: SDD — T5.4 turnos múltiples agenda
description: Vista turnosMultiples — carrito N prestaciones, mismo día, reserva lote.
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples
---

# T5.4 — Turnos múltiples

**Estado:** **gate-done** 2026-09-14 (G6 smoke ops **PASS**). Clarify **FIRME** Camino 1 — 2026-09-11. D-TUR-55 · D-TUR-56.  
Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5 **gate-done**).  
Ruta: **`/turnos/agenda`** (HIS navega a `turnosMultiples.faces`).  
Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy msg.* — G0 |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones — G0 |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Turnero + Agregar/Consultar/Asignar |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin `tmp_*` |

**No es** repetidos (misma prestación N días). **No es** la grilla de una agenda.  
**No es** `$popupCambiarTurno` — cobrado en [`turnos-agenda-multiples-cambiar/`](../turnos-agenda-multiples-cambiar/) **gate-done**.  
**No es** HOS-APP. Canon HIS: `turnosMultiples.xhtml` + `BBAgenda` (HOSPITAL_2).
