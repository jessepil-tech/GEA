---
title: SDD — T3 Turnos · grupos + horarios
description: DDL + ABM mínimo de grupos de prestación turno, horarios y días (pers/serv).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-horarios-grupos
---

# T3 — Grupos + horarios (`turnos-horarios-grupos`)

**Estado:** **gate-done** (smoke 2026-09-01) · Clarify FIRME · G1–G5.  
Hijos diferidos: [`turnos-horarios-inhibiciones/`](../turnos-horarios-inhibiciones/) · [`turnos-horarios-especiales/`](../turnos-horarios-especiales/) · T4 [`turnos-generacion-grilla/`](../turnos-generacion-grilla/).  
Hijo UI cobrado: [`turnos-horarios-pers-shell/`](../turnos-horarios-pers-shell/) (**gate-done** 2026-09-01 — west + inicio/fin).  
Capa 4: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md)

Padre capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (corte **T3** · pipeline A5).  
Prerrequisito: [`turnos-config-hab-horarios/`](../turnos-config-hab-horarios/) **T2 gate-done**.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance, Clarify, RFs, fuera de alcance |
| [plan.md](plan.md) | Capas + riesgos |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | **PASS** anti-gap + Paridad UI |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy msg.* |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones BB/API |

**No es** generación de grilla (T4) ni agenda/otorgar (T5). Es la config de **franjas** que alimenta `f_gen_grilla_turnos`.
