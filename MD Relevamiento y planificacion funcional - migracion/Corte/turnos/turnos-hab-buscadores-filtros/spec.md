---
title: Spec — Turnos hab buscadores filtros
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-hab-buscadores-filtros
---

# Spec — Filtros / columnas buscadores hab

Padre: [`turnos-hab-buscadores/`](../turnos-hab-buscadores/) (**gate-done**).  
Canon: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) · [`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md).

## Clarify — **FIRME** (2026-08-28)

| # | Pregunta | Respuesta | Evidencia |
|---|----------|-----------|-----------|
| 1 | ¿Cerrar gaps UI del dialog antes de T3? | **Sí** — paridad 1:1 del buscador usado por hab. | User 2026-08-28 |
| 2 | ¿Incluir `personal_adm_tur_serv_centro` (D-HAB-BUSC-02)? | **Sí** — hab llama buscador con `personalAdmServicioTurno=true`; combos centro/servicio filtrados. Seed DEV admin↔1001/10. | `BBHabTurnosServCentro` → `buscar(..., true, ...)` |
| 3 | ¿Selección múltiple? | **N/A hab** (single). Documentado; no WAIVE de capacidad usada por hab. | xhtml `seleccionMultiple=false` |
| 4 | ¿soloServicioCentroActivo? | Hab pasa **false**; v1 sin checkbox. Filtro opcional API `soloActivo` default false. | overload buscar hab |
| 5 | ¿Amb/int valores? | `null`=Todos · `AMBULATORIO` · `INTERNADO` (+ columna label). | `Constants` + `selectItemsAmbIntTodos` |

## Inventario

| Capacidad | Estado slice |
|-----------|--------------|
| Combo centro (serv) | In scope |
| Filtro + col amb/int | In scope |
| Apellido + nombre separados (pers) | In scope |
| Combos centro + servicio (pers) | In scope |
| Cols tipo/nro doc, apellido, nombre | In scope |
| Filtro personal_adm | In scope (D-HAB-BUSC-02) |
| Multi-select | N/A hab |

## RF

| Id | Requisito |
|----|-----------|
| RF-1 | API opciones centros (activos + filtro adm del login) y servicios por centro |
| RF-2 | GET serv-centro: `ambIntTodos` + col `ambIntTodos`/`ambIntTodosLabel` |
| RF-3 | GET pers-serv: `apellido`, `nombre` (además de q); hit con campos separados + docs |
| RF-4 | Dialog UI paridad xhtml (filtros + columnas) |
| RF-5 | Seed `personal_adm` + `amb_int_todos` DEV |
| RF-6 | Smoke + verify gate |

## Criterios

1. Dialog serv: centro combo + amb/int + columna amb/int.  
2. Dialog pers: apellido/nombre + combos + cols doc.  
3. Sin adm seed → combos vacíos/fallan; con seed admin ve 1001/10.  
4. Verify sin silencios.
