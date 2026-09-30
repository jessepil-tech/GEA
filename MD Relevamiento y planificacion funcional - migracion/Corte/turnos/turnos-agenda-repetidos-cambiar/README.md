---
title: SDD — T5.3 hijo · cambiar horario turnos repetidos
description: Popup `$popupCambiarTurno` 1200×550 — otros LIBRE del mismo día.
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar
---

# T5.3-b — Cambiar horario (turnos repetidos)

**Estado:** **gate-done** 2026-09-11 (G6 smoke ops **PASS**). Clarify **FIRME** Camino 1. D-TUR-53 · D-TUR-54.  
Padre: [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/) (T5.3 **gate-done**).  
Ruta: **`/turnos/agenda`** (vista repetidos).  
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

**No es** reserva N (T5.3) ni múltiples (`turnos-agenda-multiples`) ni el popup de `turnosMultiples.xhtml`.  
**No es** HOS-APP (`BBTurnosRepetidosHosApp`). Canon HIS: `turnosRepetidos.xhtml` + `BBTurnosRepetidos`.

Legacy: `$popupCambiarTurno` · dialog **1200×550** · header `Otros turnos disponibles`.
