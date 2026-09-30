---
title: Inventario geometría — M1a grp
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.geometria
---

# Inventario geometría — M1a grp (G0)

Firma producto **2026-09-17b:** chrome HAB (searchBar + tabla + pager + Agregar). `layoutPane` HIS **no** se porta.

| Zona | Legacy | Web v1 |
|------|--------|--------|
| North | input **500px** + Buscar 100px | `UI.list.searchBar` input 500px |
| West Agregar/Eliminar | 150px | Agregar debajo; baja por fila |
| Center nombre `(*)` | `ui-g-6` fluid (sin width) | dialog input **500px** (mismo campo que north) |
| Center checkbox | `selectBooleanCheckbox` | checkbox dialog |
| Center `leyenda` | outputText | texto bajo el form |
| South Aceptar | `Wid125px` | `dsDialogFooter` |
| Buscador overlay | 1200×550 `closable=false` | absorbido; dialog form `[closeOnBackdrop]=false` |
| Recarga | `p:ajaxStatus` | `LoadingInterceptor` + `app-loading` |
