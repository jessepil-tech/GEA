---
title: SDD — Turnos hab · buscadores servicio-centro / personal-servicio
description: Paridad buscadores de pantallas habTurnos* (deuda T2).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-hab-buscadores
---

# Turnos hab — buscadores (`turnos-hab-buscadores`)

**Estado:** **gate-done** 2026-08-28.  
**Hijo (no omitir):** [`turnos-hab-buscadores-filtros/`](../turnos-hab-buscadores-filtros/) — combos, amb/int, apellido/nombre, cols doc.  
**Padre:** [`turnos-config-hab-horarios/`](../turnos-config-hab-horarios/) (T2 **gate-done**; UI con IDs).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md) · UI: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Problema, Clarify, RFs, inventario |
| [plan.md](plan.md) | Cortes B0–B4 + capas |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate (vacío hasta smoke) |

## Problema (una línea)

En T2, Servicio / Centro / Profesional se filtran por **ID numérico**; en legacy son
**buscadores por nombre** que resuelven el vínculo y cargan la lista hab.

## Legacy

| Pantalla | Buscador | Bean |
|----------|----------|------|
| `habTurnosServCentro.xhtml` | `buscadorServicioCentro.xhtml` | `BBBuscadorServicioCentro` · `BBHabTurnosServCentro.buscarServicioCentro` |
| `habTurnosPersServ.xhtml` | `buscadorPersonalServicio.xhtml` | `BBBuscadorPersonalServicio` · `BBHabTurnosPersServ` |

Selección → setea contexto (ids + labels) → `initListaHabTurnos` / habilita Agregar.

## No es este SDD

| Capacidad | Destino |
|-----------|---------|
| ABM hab / check vigencia | T2 (hecho) |
| Sync `atiende_turnos` | D-TUR-11 / pendientes |
| ABM equipo hab | D-TUR-12 |
| T3 horarios/grupos | `turnos-horarios-grupos` |
| Buscadores genéricos de todo el hospital | Reutilizar componente si nace aquí; no ensanchar a todos los xhtml |
