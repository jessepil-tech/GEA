---
title: SDD — Paridad orientación Hospital-Web (shell)
description: Un corte de catálogo/home. Tile→módulo, hab TURNOS 15411, satélites, hojas texto.
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.paridad-orientacion-web
---

# Paridad orientación Web (`paridad-orientacion-web`)

**Estado:** **gate-done** (W6 2026-08-31). Corte **único** de shell: no se
repite por módulo. Hijo gate recepción sigue abierto.  
**Capa 3:** [`relevamiento-his-orientacion/`](../../../relevamiento/relevamiento-his-orientacion/) (O1
**hecho**).  
**Regla:** [`regla-paridad-orientacion-visual.md`](../../../canon/regla-paridad-orientacion-visual.md).  
**Gobierno:** [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

**Hijo (no omitir):** [`paridad-recepcion-gate/`](../../recepcion/paridad-recepcion-gate/) — gate
centro/recepción/box. Turnos call center ya está (T1).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Problema, Clarify, inventario, RFs |
| [plan.md](plan.md) | Cortes W0–W6 (solo Hospital-Web) |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | GATE-DONE W6 |

## Problema (una línea)

El operador de Verona no encuentra el barrio: el tile salta a un CU; hab turnos cuelga
de Configuración; AGI/TV parecen módulos HIS.

## No es este SDD

| Capacidad | Destino |
|-----------|---------|
| Identity `GET /menus` + sync 1240 filas | M2 en [`arbol-mapeo-menu-legacy-web.md`](../../../relevamiento/arbol-mapeo-menu-legacy-web.md) |
| A–C / CU de Turnos, HC, caja, … | SDD de ese módulo |
| Copiar 37 árboles completos al catálogo | M2 / cada CU habilita su hoja |
| Look Verona (teal, 12px, MAYÚSCULAS) | **WAIVE** contrato D |
| Gate puesto Recepción | **diferido(`paridad-recepcion-gate`)** |
| Recorte prod `menuShowAll=false` | Ya existe el flag; no es este corte |
