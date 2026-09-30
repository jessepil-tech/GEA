---
title: SDD — T5 · agenda / reservar / otorgar
description: Port f_get_grilla_dia + reserva/otorga/libera/tomar/sobreturno; UI HIS agenda.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-otorgar
---

# T5 — Operación agenda (`turnos-agenda-otorgar`)

**Estado:** **gate-done** · Clarify **FIRME** 2026-09-07 · G0–G6 (e2e PASS; smoke ops **PASS** 2026-09-08).  
Capa 4: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md)

Padre capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (corte **T5** · pipeline **A7**).  
Prerrequisito: [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) **T4 gate-done**.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance, **Clarify FIRME**, RFs, fuera de alcance |
| [plan.md](plan.md) | Capas + cortes G0–G6 + riesgos |
| [tasks.md](tasks.md) | Checklist (G0 bloqueado a FIRME) |
| [verify-report.md](verify-report.md) | **PASS / gate-done** · e2e 3 viajes · smoke checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy msg.* — G0 tras FIRME |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones BB/SP — G0 tras FIRME |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Gaps DDL — G0/G1 |

**No es** generación de oferta (T4) ni suspender/reemplazo masivo (T6) ni SMS/BIRT turno (T7).  
Es **ocupar** slots `LIBRE` (T4) → `RESERVADO` → `OTORGADO` (consumo AGI).  
**Lock sesión:** opción A paridad legacy (D-TUR-28) — tomar + poll 59 s + expiración 3/30 min.

**UI HIS canónica:** `asignacionTurnos/agenda.xhtml` + template `asignacionTurnos.xhtml` (`BBAgenda` / `BBAsignacionTurnos`).  
**No clonar** el puente HOS-APP `pages/agenda/inicioAgenda.xhtml` (`BBInicioAgenda` → `loginByPass`).

Legacy package: `Packages/Turnos/TURNOS.PACKAGE*.sql`.
