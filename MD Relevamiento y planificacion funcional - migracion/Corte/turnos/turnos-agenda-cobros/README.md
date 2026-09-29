---
title: SDD — T5.1d · cobros agenda (rechazo + pagar/saldo)
description: >-
  Efecto HIS al rechazar elegibilidad (convenio default + popup obs) y
  dialogs pagar / saldo CTA / coseguro en /turnos/agenda.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-cobros
---

# T5.1d — Cobros agenda (`turnos-agenda-cobros`)

**Estado:** **gate-done** 2026-09-10 · Clarify **FIRME** Camino 1 (**2026-09-09**).  
Padre: [`turnos-agenda-elegibilidad-cobros/`](../turnos-agenda-elegibilidad-cobros/) (T5.1c **gate-done**).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A7.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Deuda WS: [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) **P-ORA-010** (sigue abierto).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** 2026-09-10 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy dialogs |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Popup rechazo / pagar / saldo |
| [inventario-validaciones.md](inventario-validaciones.md) | BB rechazo + pagar |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Convenio dflt / CTA |

**No es** clientes WS reales (P-ORA-010).  
**No es** motor de facturación completo ni caja (montos `diferido(fixture)`).  
**No es** T6 ciclo de vida.
