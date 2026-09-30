---
title: Inventario interacción UI — T5 Turnos agenda
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-otorgar
---

# Inventario interacción UI — T5

Fuente: `agenda.xhtml` · `infoTurno.xhtml` · `BBAgenda` · `BBAsignacionTurnos`.

**Propósito:** complementar inventario copy/validaciones con **cómo** el usuario dispara cada acción.
Evita sustituir menús contextuales / icon-only por botones inline “por pragmatismo”.
Copy del `title` (`limpiar_profesional`) **no** cierra el icon-only — v1.15.

## North filtros (`agenda.xhtml`)

| Control legacy | xhtml | Disparador | Efecto BD / UI | Web T5 |
|----------------|-------|------------|----------------|--------|
| Paciente lupa | L37–39 `ui-icon-search` | Clic | Abre buscador paciente | `turnos-agenda-buscar-paciente` |
| Paciente info | L40 `fa-info` | Clic | Popup info búsqueda | `turnos-agenda-info-busqueda` |
| Paciente X | L41–44 `ui-icon-closethick` | Clic | `limpiarDatosPaciente` | `turnos-agenda-limpiar-paciente` |
| Convenio lupa | L61–63 `ui-icon-search` | Clic | Abre buscador convenio | `turnos-agenda-buscar-convenio` |
| Plan info | L75–78 `ui-icon-info` | Clic | Popup info convenio | `turnos-agenda-info-convenio` |
| Profesional X | L154–156 `ui-icon-close` | Clic | `limpiarPersonalCombo` | `turnos-agenda-limpiar-profesional` (2026-09-16) |
| Profesional info observaciones | L157–159 `ui-icon-info` | Clic (HIS no auto-abre; `visible` solo habilita chrome) | `infoObservaciones` · `popupInfoObservaciones` | `turnos-agenda-info-observaciones` (2026-09-16) |
| Prestación lupa | L192–194 `ui-icon-search` | Clic | Abre buscador prestación | `turnos-agenda-buscar-prestacion` |
| Prestación X | L195–199 `ui-icon-closethick` | Clic | `limpiarDatosPrestacion` | `turnos-agenda-limpiar-prestacion` (2026-09-16) |
| Prestación info | L200–203 `ui-icon-info` | Clic | `infoPrestacion` · `popupInfoPrestacion` | `turnos-agenda-info-prestacion` (2026-09-16) |

Convenio HIS **no** tiene X (solo lupa) — no inventar. Volver del buscador convenio
**sí** vacía el campo (`setConvenioBuscado(null)` — v1.17). Prestación/profesional:
Volver del buscador = `set*Buscado(null)` (distinto del X de campo). Paciente Volver
conserva. Buscadores: `closable="false"` HIS → `closeOnBackdrop=false`; overlay no
cierra por drag-select (v1.18).

Prestación info: Imprimir preparación **visible disabled** (HIS sin recepción / T7). Combo rango de edades sin paciente = seed CONS 1 fila NULL-NULL; HTML vía GET `prep-req` (edad 0). Requisitos se cargan del mismo GET (el xhtml los muestra; el BB de Agenda no los seteaba).

## Grilla día (`tablaTurnos`)

| Control legacy | xhtml | Disparador | Efecto BD / UI | Web T5 |
|----------------|-------|------------|----------------|--------|
| Col acciones | `p:column width="24"` + `ui-icon-gear` + `p:tieredMenu` | Clic engranaje | Abre menú fila | `TurnosAgendaRowMenuComponent` · testid `turno-menu-*` |
| Asignar Turno | `p:menuitem` `ASIGNAR_TURNO` | Menú fila | `actionBtnReservarTurno` → reserva → popup info | Menú → `reservar()` |
| Liberar | `p:menuitem` `LIBERAR` | Menú fila | `actBtnLiberarSeleccionMotivo` → dialog motivo | Menú → `abrirLiberar()` |
| Información | `p:menuitem` `INFORMACION` | Menú fila | `actBtnInfoTurno` → popup info solo lectura | Menú → `verInformacion()` |
| Reasignar | `p:menuitem` `REASIGNAR` | Menú fila | T6.1 | **done** [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) **gate-done** 2026-09-10 |
| Turnos repetidos | `p:menuitem` | Menú fila | — | **diferido** |
| Reenviar mail / Imprimir | `p:menuitem` | Menú fila | T7 | **diferido** T7 |
| Drag paciente → celda | `p:droppable` col paciente | Drop | `onDrop` → `actionBtnReservarTurno` | **diferido** `turnos-agenda-drag-drop` |
| Selección fila | `selectionMode="single"` | Clic fila | Selección visual; **no** reserva sola | N/A v1 |

## Modal info turno (`infoTurno.xhtml`)

| Control | Disparador | Efecto | Web T5 |
|---------|------------|--------|--------|
| Asignar Turno | Botón footer (solo si `otorgaTurno`) | `actionBtnOtorgarTurno` | `modoOtorgar=true` · `turnos-info-turno-otorgar` |
| Cancelar | Botón footer (modo otorga) | `actionBtnLiberarTurno` libera RESERVADO | `onCerrarInfoTurno()` → `cancelarInfoTurno()` |
| Volver / Cerrar | Botón footer (solo lectura) | Cierra sin liberar | `modoOtorgar=false` · label `cerrar` |
| Poll 59 s | `p:poll` | `actionTomarTurno` | `modoOtorgar` activo |

## Anti-regresión (lección T5 2026-09-07)

| Error | Síntoma | Regla |
|-------|---------|-------|
| Botones inline Asignar/Liberar en grilla | Usuario cree que legacy no reserva al “asignar”; disposición ≠ xhtml | **Prohibido** sin `diferido(slug)`. Acciones de fila = inventariar **disparador** del xhtml |
| Toast “asignado” al reservar | Confunde reserva vs otorga | Toast éxito solo en **otorga** |
| Cancelar modal sin liberar | Turno queda RESERVADO | Cancelar en modo otorga = **liberar** (paridad legacy) |

## Verify

Fila obligatoria en [verify-report.md](verify-report.md): **Inventario interacción** ↔ componentes Web.
E2E debe usar el **mismo disparador** que legacy (menú gear, no botón suelto en grilla).
