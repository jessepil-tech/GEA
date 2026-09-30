---
title: SDD — T5.1c · elegibilidad north + doc req
description: >-
  Validar elegibilidad en /turnos/agenda (afiliado/doc, icono, spinner) y
  filas de documentación requerida en info convenio. WS real y cobros diferidos.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros
---

# T5.1c — Elegibilidad north (`turnos-agenda-elegibilidad-cobros`)

**Estado:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 (**2026-09-08**).  
Padre: [`turnos-agenda-info-popups/`](../turnos-agenda-info-popups/) (T5.1b **gate-done**).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A7.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Deuda WS: [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) **P-ORA-010** (sigue abierto).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** 2026-09-10 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy north + dialogs |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Afiliado / icono / spinner |
| [inventario-validaciones.md](inventario-validaciones.md) | BB `validarElegibilidad` |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `ts.doc_req_*` + validador |

**No es** clientes WS reales de obras sociales (P-ORA-010 sigue abierto).  
**No es** motor de caja. Popup rechazo + convenio default → [`turnos-agenda-cobros/`](../turnos-agenda-cobros/) **gate-done**.  
**No es** T6 ciclo de vida.
