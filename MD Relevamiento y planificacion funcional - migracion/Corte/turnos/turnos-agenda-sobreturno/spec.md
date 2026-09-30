---
title: Spec — T5.2 sobreturno agenda
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno
---

# Spec — Sobreturno desde agenda

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5 · RF-7 API **done** · UI incompleta).  
Ruta: **`/turnos/agenda`** (no fork).  
D-TUR-45 · D-TUR-46 **Camino 2**.

## Problema

T5 portó `POST /api/v1/turnos/agenda/sobreturno` (`f_reserva_sobreturno_pac`) e IT. En Web **no** hay el acto HIS: menú west **Sobreturno** → `$popupSobreturno` → Aceptar reserva `sobreturno='S'` → mismo `infoTurno` de otorga. El call center no puede dar un turno fuera de un slot `LIBRE`. HIS no es una sola columna west: hay accordion **170px** (Paciente | Turnero) + calendario 260px + grilla.

## Resultado (objetivo Camino 2)

Misma ruta. Accordion HIS 170px. Disparador **Sobreturno** (Turnero, no gear). Popup HIS. Aceptar: `INSERT ts.turno` `RESERVADO` + `sobreturno='S'` (API T5) → dialog info turno T5 → otorgar. Liberar un sobreturno **sigue T5** (DELETE). Ítems no cobrados: **visibles disabled** + tooltip `diferido(slug)` — no silencio.

## Clarify — **FIRME** Camino 2 (2026-09-10)

Firma producto: **ok firme**. Gate UI xhtml **antes** de template.

| # | Pregunta | Respuesta FIRME | Evidencia |
|---|---------|-----------------|-----------|
| 1 | ¿Pipeline previo? | T5 **gate-done** (grilla / reserva / otorga / libera + **API** sobreturno). Call center T1. Hab T2. Oferta T4. Motivo `SOBRETURNO` seed V35 `91002`. | A7 T5 · V31 `id_motivo_sobreturno` · V35 |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. **NO fork.** Accordion **170px** HIS (`asignacionTurnos.xhtml` L20–108) **sí** en esta hoja. ABM tab Paciente **no** (disabled + slug). | T5 D-TUR-22 · Camino 2 |
| 3 | ¿Happy path? | Disparador: ítem **Sobreturno** en accordion Turnero (L72–75: `actBtnSobreturno`). Sin paciente → toast `Debe seleccionar un paciente.` Popup: prestación (*)+lupa (reusa T5); fecha `mindate` hoy; hora desde/hasta default **now trunc 5 min + 30**; centro/servicio `InputWid100` (defaults north); profesional+lupa; **equipo visible disabled** D-TUR-17; paciente readonly; motivo `GET …/motivos?tipoMotivo=SOBRETURNO` si hay filas. Aceptar: validar → si el paciente ya tiene turnos ese día → `$popUpInfoTurnosDeHoy` → `POST …/sobreturno` → infoTurno T5. Volver popup: cierra **sin** INSERT. Popup usa **estado local** (no pisa horas west del calendario). | xhtml L316–444 · `BBAgenda` ~2841–3052 · `agenda.xhtml` L600–617 |
| 4 | ¿Ciclo de vida? | INSERT `RESERVADO` `sobreturno='S'`; lock sesión T5; otorga → `OTORGADO`; libera T5 **DELETE** (no vuelve `LIBRE`). Expirar reserva sobreturno **30 min** ya T5. | T5 RF-6/RF-7/RF-8 |
| 5 | ¿Errores / permisos? | Prestación / centro / servicio requeridos; fecha ≥ hoy; si fecha = hoy, hora desde ≥ ahora. Call center T5. Toast **sin** `/500`. El adapter T5 **INSERT**a el sobreturno (no exige grilla LIBRE T4). `NO_SE_PUEDE_ASIGNAR_SOBRETURNO` queda en MessageBundle Oracle; UI no lo gatilla. Lista espera / hab `permiteDarTurnoPersAteAmb` → **fuera**. | `BBAgenda` ~2903–2964 · `MessageBundle` |
| 6 | ¿Side-effects? | Mail/SMS/BIRT → **T7**. `hist_turno` al **otorgar** reusa T5. Tope `ctrl_turnos_pac` SOBRETURNOS → **diferido** `turnos-ctrl-ctd-max-pac`. | T7 · D-TUR-13 |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Popup: header `sobreturno`, `closable=false`, sin X, modal. Labels col ~111px; prestación fila input+lupa; horas misma fila; Aceptar/Volver `MarAuto` 2 cols, **no** `authPrimary`. Accordion 170px + Acciones/Volver south. **No** Sobreturno en gear. | `regla-paridad-ui-legacy` |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) sin paciente → toast, no popup; (b) con paciente → abre popup; (c) Aceptar → infoTurno otorga. Turnos-de-hoy: **e2e-migrado** (fixture OTORGADO mismo paciente). Legacy HIS **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿Equipo / otras hojas / T6? | Equipo usable **fuera** D-TUR-17 (visible disabled). Múltiples / consulta / cola / pre-agenda / hist / tab Paciente ABM: **visibles disabled** + tooltip slug (no WAIVE). Hoja Turnero «Paciente» **no render** (`rendered=false`). Avisos: disabled como HIS. T6 suspender fuera. | slugs abajo |

**Accordion Turnero (esta hoja)**

| Ítem | Este slice |
|------|------------|
| Agenda | Activo (página actual, highlight) |
| Sobreturno | Activo → `$popupSobreturno` |
| Múltiples | Visible disabled `diferido(turnos-agenda-multiples)` |
| Consulta Agenda | Visible disabled `diferido(turnos-agenda-consultas)` |
| Reasignación (cola) | Visible disabled `diferido(turnos-ciclo-vida)` |
| Pre-agenda | Visible disabled `diferido(turnos-agenda-preagenda)` |
| Historial Turnos | Visible disabled `diferido(turnos-ciclo-vida)` |
| Avisos | Disabled como HIS (`disabled=true`) · `diferido(turnos-agenda-avisos)` |
| Paciente (hoja Turnero) | **No render** (`rendered=false`) |

**Accordion Paciente:** 10 ítems visibles disabled `diferido(turnos-asignacion-shell)`.  
**South Acciones / Volver:** **fuera** (producto 2026-09-10 — breadcrumb ya cubre volver).

**Fuera de este slice (capacidad, no chrome):** ABM `datosPaciente`; equipo usable; tope SOBRETURNOS; T7; lista espera; HOS-APP; otras `.faces`.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Accordion 170px chrome | `asignacionTurnos.xhtml` L20–108 | **In scope** (Camino 2) | No |
| Menú Turnero Sobreturno | L72 | **In scope** | No |
| Ítems diferidos visibles disabled | L24–94 | **In scope** (chrome + tooltip slug) | No |
| Volver south accordion | L100–108 | **In scope** → `/turnos/inicio` | No |
| Popup `$popupSobreturno` | L316–444 | **In scope** | No |
| Default hora +30 min | `actBtnSobreturno` ~2841 | **In scope** | No |
| Aceptar → reserva `sobreturno='S'` | `otorgarSobreTurno` · `f_reserva_sobreturno_pac` | **In scope** (API T5) | Sí INSERT |
| Cierre otorga infoTurno | `actionBtnOtorgaTurno` | **In scope** (reusa T5) | Sí UPDATE |
| Popup turnos de hoy | `$popUpInfoTurnosDeHoy` | **In scope** (camino Aceptar) | No |
| Liberar sobreturno DELETE | T5 | **Hecho T5** | — |
| Equipo combo usable | xhtml L401 | **diferido** D-TUR-17 (visible disabled) | — |
| Tope `ctrl_turnos_pac` SOBRETURNOS | `pp_ctrl_turnos_pac` | **diferido** `turnos-ctrl-ctd-max-pac` | — |
| ABM tab Paciente | `datosPaciente.faces` | **diferido** `turnos-asignacion-shell` | — |
| Mail/imprimir | gear agenda | **diferido** T7 | — |

## Requisitos (Camino 2)

| Id | Requisito |
|----|-----------|
| RF-1 | Sin paciente: toast `Debe seleccionar un paciente.`; no abre popup. |
| RF-2 | Con paciente: popup HIS; defaults north + horas now+30. |
| RF-3 | Aceptar válido: INSERT `RESERVADO` `sobreturno='S'` + abre infoTurno otorga T5. |
| RF-4 | Volver popup: cierra; **no** INSERT. |
| RF-5 | Gate UI xhtml **antes** de template. Inventarios G0. |
| RF-6 | e2e-migrado: toast sin paciente; abre popup; otorga sobreturno; turnos de hoy. |
| RF-7 | Accordion 170px misma ruta: Agenda+Sobreturno vivos; resto visible disabled + tooltip slug; Paciente hoja Turnero no render. South Acciones/Volver **fuera** (producto). |
| NFR-1 | Reusar `POST …/sobreturno` + otorga T5; Resource delgado. |
| NFR-2 | Sin fork `/turnos/agenda`. Error API sin `/500`. |
| NFR-3 | Equipo no habilitar (D-TUR-17). |

## Decisiones (FIRME)

| Id | Decisión |
|----|----------|
| D-TUR-45 | Slug `turnos-agenda-sobreturno` cobra **UI** del popup + disparador Turnero + chrome accordion 170px. API T5 se reusa. Equipo / tope / ABM Paciente / T7 / otras `.faces` fuera (chrome disabled, no silencio). |
| D-TUR-46 | **Camino 2:** accordion HIS 170px en `/turnos/agenda`; Sobreturno live; no fork. `popUpInfoTurnosDeHoy` **sí** en Aceptar sobreturno. |

## No objetivos

| Ítem | Destino |
|------|---------|
| API INSERT sobreturno | T5 (hecho) |
| Liberar / lock / hist otorga | T5 (hecho) |
| Equipo usable | D-TUR-17 |
| Tope cantidad sobreturnos | `turnos-ctrl-ctd-max-pac` |
| ABM Paciente / otras hojas Turnero | slugs diferidos (chrome visible disabled) |
| Mail / imprimir | T7 |

## Evidencia legacy (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/asignacionTurnos.xhtml L20–108 · L72–75 · L316–444
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/agenda.xhtml L267–268 (col motivo) · L600–617 (turnos hoy)
Hospital-Legacy/HOSPITAL_2/src/.../BBAgenda.java actBtnSobreturno ~2841 · actBtnOtorgarSobreturno ~2903 · otorgarSobreTurno ~2990
Hospital-Legacy/HOSPITAL-BUSINESS/.../MessageBundle.java DEBE_SELECCIONAR_UN_PACIENTE · NO_SE_PUEDE_ASIGNAR_SOBRETURNO
Hospital-API POST /api/v1/turnos/agenda/sobreturno · GET /api/v1/turnos/motivos?tipoMotivo=SOBRETURNO
```
