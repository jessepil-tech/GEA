---
title: SDD — T5.2 · sobreturno agenda
description: >-
  Accordion HIS 170px + popup Sobreturno en /turnos/agenda: INSERT turno
  sobreturno='S' RESERVADO y mismo flujo otorga T5. API T5 ya existe.
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno
---

# T5.2 — Sobreturno agenda (`turnos-agenda-sobreturno`)

**Estado:** **gate-done** 2026-09-10 · Clarify **FIRME Camino 2** (**2026-09-10**). G6 smoke ops **PASS**.  
Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5 · API `POST …/sobreturno` **done**; UI parcial).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A7.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).

Ruta: **`/turnos/agenda`** (NO fork). Layout: **[accordion 170px] [calendario 260px] [grilla]**.

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME Camino 2** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy popup / accordion / toast |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Accordion + dialog |
| [inventario-validaciones.md](inventario-validaciones.md) | BB `actBtnSobreturno` / `actBtnOtorgarSobreturno` |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `id_motivo_sobreturno` ya V31; motivo seed V35 |

**No es** ABM `datosPaciente` (chrome disabled).  
**No es** múltiples / pre-agenda / cola / historial (chrome disabled + slug).  
El combo Equipo quedó en [`turnos-agenda-sobreturno-equipo`](../turnos-agenda-sobreturno-equipo/).  
**No es** T7 mail/SMS/BIRT.  
**No es** lista espera (`personalListaEspera`).
