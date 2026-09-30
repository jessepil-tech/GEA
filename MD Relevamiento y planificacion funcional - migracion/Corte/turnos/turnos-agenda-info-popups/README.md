---
title: SDD — T5.1 hijo · popups info north agenda
description: >-
  popupInfoBusqueda + popupInfoConvenio + auto observaciones convenio/plan
  en /turnos/agenda. Lectura; sin WS elegibilidad.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-popups
---

# T5.1 hijo — Info popups north (`turnos-agenda-info-popups`)

**Estado:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 **2026-09-08**.  
Padre: [`turnos-agenda-ficha-paciente/`](../turnos-agenda-ficha-paciente/) **gate-done** 2026-09-08.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A7.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** 2026-09-10 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy dialogs |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Botones + disparadores |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones (mínimas) |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Doc req filas → T5.1c |

**No es** validador WS (P-ORA-010). Seed + filas doc req → [`turnos-agenda-elegibilidad-cobros/`](../turnos-agenda-elegibilidad-cobros/) **gate-done**.  
**No es** auto-popup obs (WAIVE D-TUR-39).
