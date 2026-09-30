---
title: Inventario interacción UI — T5.2 sobreturno
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno
---

# Inventario interacción UI — Sobreturno (G0)

Fuente: `asignacionTurnos.xhtml` L20–108 · L72–75 · L316–444 · `agenda.xhtml` L600–617 · `BBAgenda`.

Complementa T5 [`inventario-interaccion-ui.md`](../turnos-agenda-otorgar/inventario-interaccion-ui.md) (gear grilla — **no** meter Sobreturno en el gear).

## Accordion 170px (Camino 2 FIRME)

HIS: `pe:layoutPane west size="170"` · `p:accordionPanel multiple activeIndex="0,1"` · south Acciones/Volver.

Layout Web: **[accordion 170] [west calendario 260] [grilla]** — misma `/turnos/agenda`.

| Control legacy | Disparador | Efecto | Web |
|----------------|------------|--------|-----|
| Tab Paciente (10 menuitem) | click → `datosPaciente.faces` | ABM ficha | **Visible disabled**; tooltip `diferido(turnos-asignacion-shell)` |
| Turnero Agenda | click → `agenda.faces` | carga agenda | Página actual; **highlight** `gt-turnos-west-nav-active` |
| Turnero Sobreturno | click `actBtnSobreturno`; `disabled` si `idPMI ne 'Agenda'` | Sin paciente: toast WARN; con paciente: popup | Ítem habilitado (siempre Agenda); **no** gear |
| Turnero Paciente hoja | `rendered=false` | — | **No render** |
| Turnero Múltiples | `turnosMultiples.faces` | otra hoja | Visible disabled `diferido(turnos-agenda-multiples)` |
| Turnero Consulta Agenda | `consulta.faces` | otra hoja | Visible disabled `diferido(turnos-agenda-consultas)` |
| Turnero Avisos | `disabled=true` HIS | — | Disabled `diferido(turnos-agenda-avisos)` |
| Turnero Reasignación cola | `turnosAReasignar.faces` | T6 cola | Visible disabled `diferido(turnos-ciclo-vida)` |
| Turnero Pre-agenda | `preAgendaTurnos.faces` | otra hoja | Visible disabled `diferido(turnos-agenda-preagenda)` |
| Turnero Historial | `historialTurno.faces` | hist | Visible disabled `diferido(turnos-ciclo-vida)` |
| South Volver | `actBtnvolver` | sale del turnero | **Fuera** (producto 2026-09-10: no tiene sentido; breadcrumb ya vuelve a inicio) |

Ambos tabs **abiertos** por defecto (HIS `activeIndex="0,1"`).

## Popup `$popupSobreturno`

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Prestación input `width:97%` + lupa 30px | change / click | `buscarPrestacionSobreturno` → buscador T5 | Reusar `PrestacionBuscadorDialog` (display+lupa T5) |
| Fecha calendar `readonlyInput` `mindate=hoy` | select | `fechaSeleccionada` | `input type=date` min hoy; no tipear libre |
| Hora desde 45px | change / timeSelect | `onChangeHoraDesde` → hasta = desde+30 | `app-grilla-time-picker` T4/T5.1 |
| Hora hasta 45px | change | rango | idem |
| Centro `InputWid100` min 150 | change | `actChangeConfiguracionSobreTurno` | Combo (lista north) |
| Servicio misma fila | change | idem | Combo T5 |
| Profesional input 80% + lupa | change / click | `buscarPersonal` | Reusar hab buscador T5 |
| Equipo combo | change | `actChangeSobreturnoEquipo` | **Visible disabled** D-TUR-17 |
| Paciente input 98% | — | `disabled=true` | Readonly north |
| Motivo combo | change | `idMotivo` | `GET motivos?tipoMotivo=SOBRETURNO`; fila solo si hay items |
| Aceptar | `actBtnOtorgarSobreturno` | Validar → quizás turnos hoy → INSERT + infoTurno | POST T5 + dialog T5 |
| Volver | `actBtnVolverSobreturno` | Cierra; HIS reset horas west 00:00–23:59 | Sin INSERT; **estado local** (west calendario no se pisa) |

Chrome: `header=sobreturno`, `closable=false`, `modal`, sin maximizar. Footer `h:panelGrid columns=2 styleClass=MarAuto`. Label col **111px**.

## `$popUpInfoTurnosDeHoy` (solo camino Aceptar sobreturno)

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Texto turnos pactados | `tieneTurnosHoy` | info | Copy Resources |
| Aceptar | `actionBtnAceptarMensajeTurnosDeHoy` | sigue `otorgarSobreTurno` | Continúa POST |
| Cerrar | `actionBtnCerrarMensajeTurnosDeHoy` | aborta; popup sobreturno sigue | No INSERT |

Detección Web: `GET …/grilla` del día popup; alguna fila `idPaciente` + `RESERVADO`/`OTORGADO`.

## Anti-regresión

| Error | Regla |
|-------|-------|
| Fork `/turnos/sobreturno` | Prohibido |
| Ocultar ítems Turnero/Paciente | Prohibido — visibles disabled + slug |
| Render hoja Turnero Paciente | Prohibido (`rendered=false`) |
| Sobreturno en menú gear de fila | Prohibido — HIS es Turnero |
| Pedir smoke sin popup HIS (header Sobreturno, sin X, Aceptar/Volver) + accordion 170 | Prohibido |
| Habilitar equipo | Prohibido D-TUR-17 |
| `authPrimary` extra | Prohibido |
| Navegar a `.faces` diferidas | Prohibido este slice |
