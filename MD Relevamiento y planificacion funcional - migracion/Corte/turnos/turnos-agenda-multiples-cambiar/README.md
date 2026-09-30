---
title: SDD — T5.4 hijo · cambiar horario turnos múltiples
description: Popup `$popupCambiarTurno` 1200×550 — swap LIBRE in-memory (turnosMultiples.xhtml).
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar
---

# T5.4-b — Cambiar horario (turnos múltiples)

**Estado:** **gate-done** 2026-09-14 (G6 smoke ops **PASS**). Clarify **FIRME** Camino 1. D-TUR-57 · D-TUR-58.  
Padre: [`turnos-agenda-multiples/`](../turnos-agenda-multiples/) (T5.4 **gate-done**).  
Ruta: **`/turnos/agenda`** (vista múltiples).  
Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | **PASS / gate-done** · e2e · G6 ops |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy msg.* — G0 |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones — G0 |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Icono ⇄ + rowSelect |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin Flyway |

**No es** reserva lote (T5.4) ni el popup de repetidos (`turnosRepetidos.xhtml` / T5.3-b).  
**No es** HOS-APP. Canon HIS: `turnosMultiples.xhtml` + `BBAgenda.actBtnCambiarTurnoMultiple`.

Legacy: `$popupCambiarTurno` · dialog **1200×550** · header `Otros turnos disponibles` · swap **en memoria** (no POST hasta Asignar Turno del padre).
