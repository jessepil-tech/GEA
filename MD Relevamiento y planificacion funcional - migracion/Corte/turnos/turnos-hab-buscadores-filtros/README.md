---
title: SDD — Turnos hab buscadores · filtros / columnas paridad
description: Hijo de turnos-hab-buscadores — no omitir controles legacy del dialog.
version: 0.1.0
status: deferred
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-hab-buscadores-filtros
---

# Turnos hab buscadores — filtros (`turnos-hab-buscadores-filtros`)

**Estado:** **gate-done** 2026-08-28.  
Padre: [`turnos-hab-buscadores/`](../turnos-hab-buscadores/) **gate-done**.

**Regla:** paridad 1:1 — lo que legacy tiene en el buscador **no se omite**;
si no entra en el slice padre, vive aquí como `diferido(slug)`, nunca en silencio
ni WAIVE por velocidad. Canon: [`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md).

## Alcance (inventario gaps vs padre v1)

### `buscadorServicioCentro.xhtml`

| Capacidad legacy | Padre v1 | Este hijo |
|------------------|----------|-----------|
| Texto servicio | Sí | — |
| Combo **centro atención** | API `idCentroAte` sin UI | **In scope** |
| Filtro + columna **amb/int/todos** (`ambIntTodos`) | No | **In scope** (ex D-HAB-BUSC-01) |
| Selección múltiple / select-todos | Solo otros callers | N/A hab (documentar) |

### `buscadorPersonalServicio.xhtml`

| Capacidad legacy | Padre v1 | Este hijo |
|------------------|----------|-----------|
| `q` unificado apellido/nombre/login | Sí (simplificado) | Separar **apellido** + **nombre** |
| Combo **centro** + combo **servicio** | API sin UI | **In scope** |
| Columnas **tipo doc** / **nro doc** | No | **In scope** |
| Columnas apellido/nombre separadas | Label compuesto | **In scope** |

### Relacionado (otro defer)

| Id | Nota |
|----|------|
| D-HAB-BUSC-02 | Filtro por `personal_adm_tur_serv_centro` — puede ir aquí o hijo propio en Clarify |

## Orden

1. Padre: smoke + verify gate-done.  
2. Este slug: Clarify → spec/plan/tasks → implement → verify.  
3. No marcar paridad UI del buscador como completa hasta cerrar este hijo (o WAIVE con evidencia de que legacy no aplica al caller hab).
