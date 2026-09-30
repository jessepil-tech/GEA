---
title: Inventario geometría — Lista espera atención médica
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Inventario geometría — E3

Fuente: `HOSPITAL_2/WebRoot/pages/ambulatoria/ambulatoria/listaEsperaAtencionMedica.xhtml`.

Instalación de referencia: **genérica (`cliente="TS"`)**. Flag
`seleccionaCualquierMedBoolean` **apagado** → dos panes 50/50 (cola personal + en
atención). El pane servicio (33 %) es `diferido` (mismo xhtml, otro vertical).

## Chrome

| Zona | xhtml | Web |
|------|-------|-----|
| `pageTitle` | centro `(servicio)` | breadcrumb módulo; no h1 extra |
| `headerText` | centro / servicio / ambiente (Fs16) | barra de contexto misma línea |
| `otherTopbarOptions` | flecha Volver | `page-shell` `backLink` |
| Norte | `pe:layoutPane north` · `panelGrid` 3 cols | misma fila: especialidad \| pacientes \| botones a la derecha |
| Combos norte | `InputWid100` = 100 % de la celda | `selectCompact` `w-full` en la celda, **no** 111px |
| Botones norte | `Wid200px` / `min-width: 200px` | `min-w-[200px]` |
| Centro | `height: calc(100% - 2px)` · panes `Hei100` | `layout="fill"` · overflow hidden |

## Cola personal (`tablaDemandaExterna`) — este corte

`width: calc(50% - 5px)` (flag apagado). `MarRig5px`. `rowStyleClass=Hei35px`.
`scrollable` `scrollHeight=100%`.

| Columna | width xhtml | Alineación | Web |
|---------|-------------|------------|-----|
| Especialidad | 150 (flag off) | left | `w-[150px]` |
| Paciente | resto | left | `min-w-0 flex-1` |
| Hora Turno | 45 | center | `w-[45px] text-center` |
| Espera | 40 | right | `w-[40px] text-right` |
| Ctd. Llamados | 45 | right | `w-[45px] text-right` |
| Engranaje | 55 | — | `w-[55px]` |

Icono tipo paciente: celda 25×23 px si hay path (v1 sin icono seed → no hueco).

## En atención (`tablaEnAtencion`) — visible, Llamar diferido

`width: calc(50% - 5px)`. Mismas columnas; engranaje width 25 (HIS). Web: mismo
juego de columnas para no desalinear (regla v1.13: una tabla HIS = columnas
compartidas **dentro** del pane; los dos panes son tablas hermanas, no unificar).

## Disabled

Verona `#dadada` en botones norte e ítems de menú no cobrados.
