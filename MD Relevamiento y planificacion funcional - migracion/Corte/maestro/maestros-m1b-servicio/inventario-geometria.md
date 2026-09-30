---
title: Inventario geometría — M1b servicio / vínculo
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1b-servicio.geometria
---

# Inventario geometría — M1b (G0)

Firma producto **2026-09-17b:** el chrome del ABM catálogo es el listado de
Habilitación de Turnos (barra búsqueda + tabla nativa + Agregar + pager).
El `layoutPane` HIS **no** se porta.
El contrato que queda es el ancho del campo `servicio (*)` **245px** en el dialog.

## Servicio (10002 · `servicio.xhtml`)

| Zona | Legacy | Web v1 (agenda/HAB) |
|------|--------|---------------------|
| North / buscador | input 500px + Buscar 100px | `UI.list.searchBar` + Buscar / Limpiar |
| West Agregar/Eliminar | 150px | Agregar debajo de tabla; baja por fila |
| Center `servicio (*)` | input **245px** | dialog crear/editar **245px** |
| South Aceptar | 135px | Aceptar en `dsDialogFooter` |

Dialog buscador HIS: absorbido por el listado (misma capacidad: buscar + elegir).

## Servicio centro (10204 · `servicioCentro.xhtml`)

Dialog buscador HIS absorbido por el listado HAB (`buscadorServicioCentro.xhtml`).

| Zona | Legacy | Web v1 (agenda/HAB) |
|------|--------|---------------------|
| Buscador `servicio_centro` | input **320px** | `Sz.inputSearch` 320px |
| Buscador `centro_atencion` | `p:selectOneMenu` **258px** (`mostrarCentroAte`) | `Sz.selectCentro` 258px; vacío = todos |
| Buscador `amb_int_todos` | select **130px** | `Sz.selectAmb` 130px |
| Buscar | `msg.buscar` | `UI.list.searchBar` + Buscar / Limpiar |
| Recarga tabla | HIS `p:ajaxStatus` overlay | `LoadingInterceptor` + `app-loading` (no `Cargando…` en el form) |

