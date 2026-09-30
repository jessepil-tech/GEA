---
title: Spec — T5.6 pre-agenda turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-preagenda
---

# Spec — Pre-agenda (Turnero)

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/).  
Ruta: **`/turnos/agenda`** (no fork).  
Clarify **FIRME Camino 1** — 2026-09-15 (Francisco: «ok»). D-TUR-61.

## Problema

T5–T5.5 cubren Agenda / Sobreturno / Múltiples / Consulta Agenda. El ítem Turnero **Pre Agenda Turnos** sigue `disabled` + tooltip `diferido(turnos-agenda-preagenda)`. En HIS es otra hoja del mismo shell (`preAgendaTurnos.xhtml` · `BBPreAgendaTurnos`): lista `ts.pre_agenda_turno` **PENDIENTE** y **Asignar** abre Agenda con paciente/servicio/prestación cargados.

## Resultado (objetivo Camino 1)

Misma ruta. Accordion 170px. Click **Pre Agenda Turnos** → vista pre-agenda (sin west calendario 260). North HIS → Consultar → tabla PENDIENTE. Acciones → **ASIGNAR TURNO** → vista Agenda T5 (north prefijado + consultar grilla). Al otorgar en Agenda, UPDATE `pre_agenda_turno` (`id_turno` + `OTORGADO`) para que salga de la lista. Equipo combo **visible disabled** D-TUR-17 (chrome Agenda, no esta hoja).

## Clarify — **FIRME Camino 1** (2026-09-15)

Firma producto: Francisco «ok» al corte Camino 1.

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T5 **gate-done**. T5.5 **gate-done**. Call center T1. Combos servicio T5. Buscador paciente T5.1. | A7 T5 · T5.5 verify |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. **NO fork.** Accordion 170 **sí**. West calendario Agenda **oculto** en esta vista (no está en `preAgendaTurnos.xhtml`). | `preAgendaTurnos.xhtml` composition · `asignacionTurnos.xhtml` L89–91 |
| 3 | ¿Happy path? | Turnero **Pre Agenda Turnos** → north: paciente+lupa+limpiar (buscador T5.1), servicio combo (Todos), convenio combo, plan combo (carga al cambiar convenio), fecha desde/hasta (default **hoy − 3 meses / hoy**, **sin** `mindate` hoy). Consultar → GET PENDIENTE → tabla scroll. Acciones (engranaje) → **ASIGNAR TURNO** → `vista=agenda`, session HIS: Paciente / Servicio / Prestacion / `ConsultarAgenda=S` / `PreAgenda`. | xhtml L14–122 · `BBPreAgendaTurnos` ctor + `actBtnConsultar` + `actBtnIrAGrilla` |
| 4 | ¿Ciclo de vida? | **Listar** solo `PENDIENTE`. **No INSERT** en esta hoja (alta = ATENCION / prescripción). **Asignar** no escribe aún; el UPDATE (`id_turno`, `estado_pre_agenda=OTORGADO`) corre en **T5 otorgar** si hay `idPreAgendaTurno` en estado Agenda (`ImpBusTurno` L527–530). UPDATE `det_prescrip_prest_*` (HIS L531–539) → **diferido** (tablas ambulatorio, no este xhtml). | BB + `ImpBusTurno.otorgarTurnoPac` |
| 5 | ¿Errores / permisos? | `fechaDesde` posterior a `fechaHasta` → `WRONG_INTERVAL_DATE_3`. **No** `_9` (HIS no exige ≥ hoy). Call center sesión T5. Toast **sin** `/500`. Filtros vacíos = Todos. Consultar **no** auto al abrir. | MessageBundle 2256 · `actBtnConsultar` |
| 6 | ¿Side-effects? | Asignar: estado cliente `preAgenda` + prefijo north Agenda + `cargarDiasGrillaCalcular`. Otorgar T5: UPDATE `ts.pre_agenda_turno`. Mail/SMS **N/A**. Sidecar reportes **N/A**. No embeber Reports. | `BBAgenda` L406–416 · L414–416 |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Labels servicio/convenio **111px**; paciente `InputWid100`; fechas 150px; tabla cols HIS (fecha prescripción 60px; acciones 60px). Header tabla `pre_agenda_turnos`. HIS `pageTitle` = `reasignacion_turnos` y **dos** labels `convenio` (el 2º es el combo plan) — **Camino 1: geometría HIS, copy del 2º combo = `convenio` como HIS**. **Delta D-TUR-65 (2026-09-17):** Consultar en fila propia centrada (`btnConsultarRow`), igual que Consulta Agenda migrada; HIS lo deja inline con las fechas (L78–82). Look Origin. | `regla-paridad-ui-legacy` · xhtml L17–59 · D-TUR-65 |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) Turnero abre vista; (b) fechas invertidas → toast; (c) Consultar → filas fixture PENDIENTE; (d) Asignar → vista Agenda con paciente prefijado. Legacy HIS **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿Fuera? | Alta ATENCION. Menú `consultaPreagenda.xhtml` → [`turnos-consultas-preagenda`](../turnos-consultas-preagenda/). Config `prestPreAgendaServ`. Cancelar / obs pre-agenda (esa hoja). Equipo D-TUR-17. T6. Avisos. Lista espera. HOS-APP. T5.5 PDF/Excel. | slugs abajo |

**Accordion Turnero (esta hoja)**

| Ítem | Estado |
|------|--------|
| Agenda | live (volver) |
| Pre Agenda Turnos | **live** + highlight |
| Consulta Agenda | live T5.5 (otra vista) |
| Sobreturno | disabled si no Agenda (HIS L74) |
| Equipo usable | D-TUR-17 |
| consultaPreagenda menú | [`turnos-consultas-preagenda`](../turnos-consultas-preagenda/) |

## Decisiones

| ID | Decisión |
|----|----------|
| D-TUR-61 | Camino 1: misma ruta; oculta calendario; JDBC `ts.pre_agenda_turno` PENDIENTE (no Hibernate Example); Asignar = navegar Agenda + UPDATE al otorgar T5; alta ATENCION fuera; menú consultaPreagenda hijo; copy 2º combo Convenio como HIS. |
| D-TUR-65 | Firma producto 2026-09-17 (Francisco): botón Consultar de Pre-agenda en fila propia centrada, igual que Consulta Agenda migrada. No volver a la posición inline HIS (L78–82). |

## Fuera de alcance (no silencio)

| Capacidad | Destino |
|-----------|---------|
| Alta fila (prescripción amb/int) | N/A este xhtml — ATENCION `insert into ts.pre_agenda_turno` |
| Consulta pre-agenda menú TURNOS | `turnos-consultas-preagenda` |
| Config prestaciones pre-agenda | `prestPreAgendaServ.xhtml` — no abrir SDD ahora; maestros |
| UPDATE `det_prescrip_prest_*` al otorgar | diferido (ambulatorio) |
| HOS-APP | no clonar |
| Equipo | D-TUR-17 |
