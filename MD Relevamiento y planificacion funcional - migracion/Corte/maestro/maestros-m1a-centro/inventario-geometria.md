---
title: Inventario geometría — M1a centro de atención
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1a-centro.geometria
---

# Inventario geometría — M1a (G0)

HIS menú: id **10203** · clave `centro_atencion` · `ACCION` `/pages/configuracion/centroAtencion/centroAtencion` bajo **ADMINISTRACION_GENERAL_NA** (10000).

Web: misma clave de tile; hoja `/configuracion/centros-atencion`. **No** colgar bajo TURNOS.

Firma producto **2026-09-17b:** el chrome del ABM catálogo es el listado de
Habilitación de Turnos (barra búsqueda + tabla nativa + Agregar + pager).
El `layoutPane` HIS **no** se porta. Anchos de **campo** del xhtml van al dialog.

## Shell (`centroAtencion.xhtml` · `contractDefault`)

| Zona | Legacy | Web v1 (agenda/HAB) |
|------|--------|---------------------|
| North | label + `inputText` width **500px** + Buscar width **100px** | `UI.list.searchBar` · input **500px** + Buscar (sin Limpiar: HIS no lo tiene) |
| West | accordion 1 tab Centro · muchos menuitems | **no** west; hojas extra `diferido` west |
| West south | Agregar / Eliminar **width 150px** | Agregar debajo de tabla; baja por fila |
| Center | `ui:insert contenedor` | tabla nativa Centro / Nombre / Acciones |
| Dialog buscador | 1200×550, `closable="false"` | absorbido por el listado (buscar + elegir) |

## Datos (`datosCentroAtencion.xhtml`)

Van al **dialog** crear/editar (`app-ds-dialog` xl, `closeOnBackdrop=false`). Tabla 100%. Label td **width 160px**. Campos `InputWid100` = 100% de la **celda**. **Dos campos por `tr`** (excepto grp fila 1 de 1; referencia domicilio `colspan=3`; textarea). Checkboxes activo/virtual en la celda, no stacked.

Filas in-scope v1: **todas** las de `datosCentroAtencion.xhtml` (grp → leyenda), incluido el fragmento no-virtual y la lupa depósito (`buscadorDeposito.xhtml`).

West (mensajes, concepto contable, recepción, logo, servicio por centro, sectores…) → **no pintar** (`diferido` west). Son otras hojas, no las filas de datos.

Disabled = `#dadada`. Checks: texto + checkbox hermanos.
