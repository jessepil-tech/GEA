---
title: SDD — turnos repetidos agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos
---

# Turnos repetidos

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5 **gate-done**).  
Ruta: **`/turnos/agenda`** (HIS cambia a `turnosRepetidos.faces` en el mismo layout).  
**gate-done** 2026-09-11 (G6 smoke ops **PASS**). Clarify **FIRME** Camino 1. D-TUR-51 · D-TUR-52.

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify FIRME |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Menú + popup + toasts |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Gear TURNOS REPETIDOS |
| [inventario-validaciones.md](inventario-validaciones.md) | Enable menú + mensajes reserva |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `tmp_observ_tur_rep` |

**No es** múltiples (`turnos-agenda-multiples`) ni pre-agenda.  
**No es** `popupCambiarTurno` — hijo [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/).
