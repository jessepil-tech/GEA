---
title: Inventario interacción UI — T5.1 ficha paciente
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-ficha-paciente
---

# Inventario interacción UI — T5.1

Fuente: `agenda.xhtml` · `asignacionTurnos.xhtml` · `BBAgenda` · `BBBuscadorPaciente` · `BBBuscadorConvenio`.

Complementa T5 [`inventario-interaccion-ui.md`](../turnos-agenda-otorgar/inventario-interaccion-ui.md) (grilla gear + infoTurno — **no regresión**).

## North — paciente (`formPrincipal` fila 1)

| Control legacy | Disparador | Efecto | Web T5.1 |
|----------------|------------|--------|----------|
| Input apellido | `change` ajax | `buscarPacienteAgenda()` → abre dialog si múltiples | tipear + opcional auto-buscar |
| Botón 🔍 | click | Igual | `abrirBuscadorPaciente()` |
| Botón info (fa-info) | click | `popupInfoBusqueda` imagen ayuda | dialog help |
| Botón limpiar (×) | click | `limpiarDatosPaciente` — vacía north + west + lateral | `limpiarPaciente()` |
| Input nro documento | `change` | `validarElegibilidad` / buscar por doc | editable; **sin** WS v1 |
| Input nro afiliado | `change` | `validarElegibilidad` | editable; icono validador **oculto** v1 |

## North — convenio / plan (filas 2–3)

| Control legacy | Disparador | Efecto | Web T5.1 |
|----------------|------------|--------|----------|
| Input convenio | `change` | `buscarConvenioAgenda` | tipear + dialog |
| Botón 🔍 convenio | click | Abre `popUpBuscadorConvenio` | `abrirBuscadorConvenio()` |
| Combo plan | `change` | `actChangePlanConvenio` — refresca obs | `onPlanChange()` |
| Botón info convenio | click | `popupObservacionesPlanConvenio` o `popupInfoConvenio` | dialog obs conv/plan (lectura) |

## Dialog buscar paciente (`popUpBuscadorPaciente` 1200×550)

| Control | Disparador | Efecto | Web T5.1 |
|---------|------------|--------|----------|
| Filtros apellido/nombre/doc/afiliado | Buscar | `buscarMaestroFiltro` → treeTable | filtros + lista paginada (v1: **dataTable** plana, no tree) |
| Fila resultado | `rowSelect` | Cierra dialog; completa north + ficha west + otros centros | click fila → `seleccionarPaciente()` |
| Nuevo paciente | click | `actBtnNuevoPaciente` | **diferido** — botón no render v1 |
| Cancelar | click | `actionBtnVolver` oculta popup; **no** llama `setPacienteBuscado` | `cancelarBuscadorPaciente()` — conserva selección |

## Dialog buscar convenio (`popUpBuscadorConvenio`)

| Control | Disparador | Efecto | Web T5.1 |
|---------|------------|--------|----------|
| Buscar | click | Lista convenios paginada | `buscar()` |
| Fila | `rowSelect` | Cierra; carga plan combo | `seleccionarConvenio()` |
| Volver/Cancelar | click | `setConvenioBuscado(null)` — vacía convenio/plan y grilla | `cancelarBuscadorConvenio()` |
| Colores fila | render | rojo no vigente / amarillo suspendido | clases CSS paridad |

## West — calendario + rango (`formMenuIzquierdo`)

| Control | Disparador | Efecto | Web T5.1 |
|---------|------------|--------|----------|
| Mes ant/sig | botones calendario | `mesAnterior` / `mesSiguiente` | ya T5 parcial — verificar |
| Calendario inline | `dateSelect` | `onSelectFechaTurno` + refresh grilla | T5 existente |
| TimePicker Desde | change/timeSelect | `onChangeHoraTurno` — recalendario + grilla | `horaDesde` signal |
| TimePicker Hasta | change/timeSelect | idem | `horaHasta` signal |
| Leyenda 6 colores | estático | referencia visual días | panel bajo calendario |

## West — ficha paciente (`formMenuPaciente`)

| Control | Disparador | Efecto | Web T5.1 |
|---------|------------|--------|----------|
| Panel datos | read-only | Muestra `bbAgenda.paciente.*` | `GET …/ficha` tras selección |
| Draggable ficha | drag | Drop en grilla reserva | **diferido** drag-drop |

Campos visibles (si hay valor): fullName, tipoDoc+nroDoc, FN, edad, sexo, conv/plan, afiliado, observaciones, teléfonos, mails.

## West — otros centros (`formMenuTurnosOtrosCentros`)

| Control | Disparador | Efecto | Web T5.1 |
|---------|------------|--------|----------|
| Tabla scroll 61px | carga | `selectTurnosOtrosCentros` | `GET …/otros-centros` |
| Gear fila | menú | tieredMenu | `TurnosOtrosCentrosRowMenuComponent` |
| Asignar Turno | menú | `actionBtnReservarTurno` | reusa reserva T5 |
| Consultar Agenda | menú | `actBtnConsultaOtrosCentros` — cambia filtros north | **diferido** consultas |
| Información | menú | `actBtnInfoTurno` | dialog info turno T5 |

## Accordion 170px tab Paciente

| Ítem menú | Legacy | T5.1 |
|-----------|--------|------|
| Datos paciente, grupo familiar, historial… | `actionSelectMenu` → `datosPaciente.faces` | **diferido** shell — ítems **no render** v1 |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Seguir con `idPaciente=20001` hardcode | Prohibido post-G4 — selección real north |
| Abrir dialog paciente sin limpiar estado | Limpiar debe resetear ficha + otros centros |
| Botón inline en tabla otros centros | Gear 24px como grilla center T5 |

## Verify

Fila obligatoria en [verify-report.md](verify-report.md): inventario interacción ↔ componentes Web T5.1.
