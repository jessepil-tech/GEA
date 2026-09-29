---
title: Inventario geometría — P3 ABM Anunciador / Terminal AG
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.anunciador-agi-config-abm.geometria
---

# Inventario geometría — P3 ABM (G0)

Canon: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) · [`regla-paridad-orientacion-visual.md`](../../../canon/regla-paridad-orientacion-visual.md).

HIS menú (`dump-menu-aplicacion.csv`): bajo **Centro de atención** (10202), no tile RECEPCIÓN.

| Id | Clave | ACCION |
|----|--------|--------|
| 10211 | `anunciador` | `/pages/configuracion/anunciador/anunciador` |
| 10212 | `diccionario_anunciador` | `/pages/configuracion/diccionarioAnunciador` |
| 10215 | `terminal_auto_gestion` | `/pages/configuracion/terminalAutogestion/terminalAutogestion` |
| 10216 | `terminal_triage` | **fuera v1** |

Web: satélite `menuKey: ANUNCIADOR` (ya en catalog). Hojas v1 bajo ese grupo: ABM anunciador, diccionario, terminal AG. Display TV sigue URL aparte **sin** shell. **No** colgar bajo RECEPCIÓN ni AGI kiosco.

Lista piloto `/anunciadores` (abrir display) **se sustituye/extiende** por el ABM; no dejar el copy “Piloto CU#1”.

## Shell anunciador (`anunciador.xhtml` · `contractDefault`)

| Zona | Legacy | Web v1 |
|------|--------|--------|
| North | label + `inputText` width **500px** + Buscar width **100px** | misma fila |
| West | accordion 1 tab `Anunciador` · 3 menuitems (datos / config avanzada / ambiente) **disabled** sin id | mismas 3 hojas; no 6 (serv/triage/esp fuera) |
| West south | Acciones: Agregar / Eliminar **width 150px** | no icon-only; botones con label |
| Center | `ui:insert contenedor` | |
| South (hojas datos/avanzada) | Aceptar (avanzada: + Restablecer Valores) `MarAuto` | |
| Dialog buscador | 1200×550, no maximizable | |

Hojas datos: `ui-g` fluid; fila 1: anunciador `(*)` + título `(*)` **6/6**; fila 2: minutos **6**; URL **12** readonly; leyenda **12**. Centro/sector **comentados** → no pintar.

Config avanzada: sección General **12**; cuatro check/combo **3+3+3+3**; URL multimedia **12**; sección cola **12**; ctd char **3** + tipo llamado **6**; sección lugar **12**; ctd char **3**; logo/fondo headers **6+6**; previews **6+6** (logo 160×160; fondo 264×160); leyenda **12**; south 2 botones.

Ambientes: `dataTable` scroll 100%; cols ambiente / sector / centro / acciones **width 50** icon trash.

## Shell terminal (`terminalAutogestion.xhtml`)

| Zona | Legacy | Web v1 |
|------|--------|--------|
| North | grid: nombre input **300px** + Buscar; **misma grid** centro **300px disabled** | 2 filas o wrap; no omitir centro readonly |
| West | 2 menuitems: terminal / opciones | |
| West south | Agregar / Eliminar **150px**; Eliminar con `p:confirm` | |
| Datos | `table` 100%: **2 campos por `tr`** (label + td **40%** `InputWid100`) | misma geometría 2-up: nombre+centro; sector+ambiente; cant opciones+activo; imprime+url |
| Centro | input + **icon-only search** (no botón “Buscar” texto) | icon-only + dialog centro |
| Dialog buscador terminal | 1200×550 | |
| Popup opción | width **800**; `panelGrid columns="4"` (label+campo ×2 por fila) | 2-up; no stacked 1 col “por pragmatismo” |

## Diccionario (`diccionarioAnunciador.xhtml`)

Sin north/west: **center full** `dataTable` scroll 100%; filtro contains palabra/equivalente; acciones width **100** (lápiz + trash). Botón Agregar `Wid150px`. Popup form palabra/equivalente + Aceptar/Cancelar `Wid150px`. Botones `escuchar` `rendered="false"` → no pintar.

## Chrome Web

Breadcrumb = menú real ANUNCIADOR → hoja. Sin `[title]` duplicado. Dark + hooks `gt-*` si hay clases dinámicas. No copiar filtros HAB/turnos.
