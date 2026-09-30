---
title: SDD — T6.1 · reasignar turno desde agenda
description: >-
  Menú fila REASIGNAR / CANCELAR_REASIGNACION en /turnos/agenda:
  sesión TurnoAReasignar, overlay PENDIENTE_LIBERAR, cierre otorga+libera origen.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-reasignar
---

# T6.1 — Reasignar agenda (`turnos-agenda-reasignar`)

**Estado:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 (**2026-09-09**, “ok firme”).  
Padres: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5 · **D-TUR-26**) · A8 T6 `turnos-ciclo-vida` (padre **diferido** para el resto).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A8.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).

Ruta: **`/turnos/agenda`** (NO fork).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** 2026-09-10 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy menú / toast / popup |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Gear REASIGNAR / overlay / popup |
| [inventario-validaciones.md](inventario-validaciones.md) | BB reasignar + otorgar |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin columna `PENDIENTE_LIBERAR` |

**No es** cola `turnosAReasignar.xhtml` — hijo T6.2 [`turnos-agenda-cola-reasignar/`](../turnos-agenda-cola-reasignar/) **gate-done** 2026-09-17.  
**No es** suspender grilla ni reemplazo profesional.  
**No es** job vencidos / pantalla `hist_turno`.  
**No es** T7 mail/SMS/BIRT.
