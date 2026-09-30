---
title: SDD — T4 · generación de grilla de turnos
description: Port f_gen_grilla_turnos / f_elim_grilla_turnos + consulta agendas; materializa ts.turno LIBRE.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.turnos-generacion-grilla
---

# T4 — Generación de grilla (`turnos-generacion-grilla`)

**Estado:** **gate-done** 2026-09-04 · G1–G6 (smoke stack real ops).  
Capa 4: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md)

Padre capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (corte **T4** · pipeline A6).  
Prerrequisito: [`turnos-horarios-grupos/`](../turnos-horarios-grupos/) **T3 gate-done**.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance, **Clarify FIRME**, RFs, fuera de alcance |
| [plan.md](plan.md) | Capas + cortes G0–G6 + riesgos |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Anti-gap **gate-done** 2026-09-04 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy msg.* (G0) |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones BB/SP (G0) |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Gaps DDL — G1 aplicado |
| [../turnos-consulta-agendas-drilldown/](../turnos-consulta-agendas-drilldown/) | Hijo drill-down calendario+turnos |
| [../turnos-consulta-agendas-imprimir/](../turnos-consulta-agendas-imprimir/) | Hijo imprimir PDF BIRT |

**No es** agenda/reservar/otorgar (T5) ni suspender/reemplazo masivo (T6). Es materializar **oferta** (`ts.turno` estado `LIBRE`) a partir de T2+T3.

Legacy: `generacionGrillaTurnos.xhtml` · `eliminarGrillaTurnos.xhtml` · `consultaAgendasGeneradas.xhtml` · `Packages/Turnos/TURNOS.PACKAGE*.sql`.
