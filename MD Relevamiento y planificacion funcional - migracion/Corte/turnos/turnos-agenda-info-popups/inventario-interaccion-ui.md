---
title: Inventario interacción UI — T5.1 hijo info popups
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-info-popups
---

# Inventario interacción UI — Info popups (G0)

Fuente: `agenda.xhtml` · `asignacionTurnos.xhtml` · `BBAgenda`.  
Complementa T5.1 [`inventario-interaccion-ui.md`](../turnos-agenda-ficha-paciente/inventario-interaccion-ui.md).

## North — paciente

| Control legacy | Disparador | Efecto | Web |
|----------------|------------|--------|-----|
| `p:commandButton` `fa-info` `type="button"` id `infoBusqueda` | click | `PF('$popupInfoBusqueda').show()` — **sin** ajax | `abrirInfoBusqueda()` |
| Dialog `popupInfoBusqueda` | Escape / X | Cierra | `infoBusquedaOpen.set(false)` |
| Imagen `InfoBusquedaPac.png` | render | Help visual 968×612 | `/images/turnos/InfoBusquedaPac.png` |

## North — plan

| Control legacy | Disparador | Efecto | Web |
|----------------|------------|--------|-----|
| `p:commandButton` `ui-icon-info` `infoConvenio` | click | Carga `listDocReq` + `displayInfoConvenio=true` | `abrirInfoConvenio()` — chrome tabla vacía; filas diferidas |
| Clase `ui-state-error` | render | Si `convenio.descripcion` o `plan.observaciones` no vacío | highlight botón |
| Dialog `popupInfoConvenio` | X / closable | Cierra | `infoConvenioOpen.set(false)` |

## Auto observaciones (template padre)

| Control legacy | Disparador | Efecto | Web |
|----------------|------------|--------|-----|
| Tras buscar convenio / `actChangePlanConvenio` | ajax `update` del dialog | HIS **no** pone `visible=true` | **WAIVE** — no auto-abrir |
| `showObservacionesConvenio()` | sin callers en Agenda | Dead code | no cablear |
| Botón `cerrar` | click | `actBtnCerrarObservacionesPlanConvenio` | N/A Agenda |
| `closable=false` | — | Solo Cerrar | N/A Agenda (dialog no se abre) |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Poner info **fuera** de la fila lupa/plan | Prohibido |
| `@Output() select` en dialog | Prohibido (evento nativo texto) |
| Pedir smoke sin botones en geometría xhtml | Prohibido |
| Omitir panel Documentación requerida en `popupInfoConvenio` | Prohibido (HIS siempre lo muestra; filas pueden estar vacías) |
