---
title: Inventario geometría — M1c especialidad
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1c-especialidad.geometria
---

# Inventario geometría — M1c (G0)

Firma producto **2026-09-17b:** chrome HAB (searchBar + tabla + pager + Agregar). `layoutPane` HIS **no** se porta.

| Zona | Legacy | Web v1 |
|------|--------|--------|
| North | input **500px** + Buscar 100px | `UI.list.searchBar` |
| West Agregar/Eliminar | 150px | Agregar debajo; baja por fila |
| Center `especialidad (*)` | **245px** | dialog **245px** |
| Center interconsulta | checkbox | checkbox dialog |
| Center `adulto_pediatrico (*)` | select **245px** | select dialog **245px** |
| South Aceptar | 135px | `dsDialogFooter` |
| Recarga | `p:ajaxStatus` | `LoadingInterceptor` + `app-loading` |

Dialog buscador HIS: absorbido por el listado. Buscador `valor` 970px no se clona en HAB.
