---
title: SDD — T2 Turnos · habilitación (hab_turnos_*)
description: DDL + gate vigencia + lectura/ABM mínimo de HAB_TURNOS_SERV_CENTRO y PERS_SERV.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-config-hab-horarios
---

# T2 — Habilitación turnos (`turnos-config-hab-horarios`)

**Estado:** **gate-done** (smoke 2026-08-27) · Clarify FIRME · H1–H5  
Hijo deuda UI: [`turnos-hab-buscadores/`](../turnos-hab-buscadores/) (**gate-done** 2026-08-28) → filtros [`turnos-hab-buscadores-filtros/`](../turnos-hab-buscadores-filtros/).  
Capa 4: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md)

Padre capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (corte **T2**).  
Prerrequisito: [`turnos-maestros-personal/`](../turnos-maestros-personal/) **T1** (personal / call center).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance, Clarify, RFs, fuera de alcance |
| [plan.md](plan.md) | Capas + riesgos |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | **PASS** anti-gap + Paridad UI |

**No es** agenda ni generación de grilla. Es la config que **autoriza** oferta (A4 del pipeline) antes de horarios (T3) y `f_gen_grilla_turnos` (T4).

Slug del relevamiento: `turnos-config-hab-horarios` (T2). T3: [`turnos-horarios-grupos/`](../turnos-horarios-grupos/).
