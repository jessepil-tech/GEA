---
title: SDD — T6.4 · suspender / quitar suspensión de grilla
description: >-
  Menú Agenda turnos: suspenderGrillaTurnos + quitarCancelacionGrillaTurnos.
  Port f_suspender_turnos / f_quitar_suspension. Equipo D-TUR-17.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender
---

# T6.4 — Suspender grilla (`turnos-grilla-suspender`)

**Estado:** **gate-done** 2026-09-18 · Clarify **FIRME Camino 1** D-TUR-72 (Francisco).  
Padre T6 `turnos-ciclo-vida` (resto **diferido**: reemplazo / vencidos).  
Hermano T4 [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) (mismo menú `agenda_turnos`).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A8.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Instalación de referencia: Call Center Demo. `BBSuspenderGrillaTurnos` / `BBQuitarCancelacionGrillaTurnos` **sin** `esClienteX()`.

Índice (2026-09-18): `--semilla suspenderGrillaTurnos` 6/8 xhtml · `--semilla quitarCancelacionGrillaTurnos` 4/8. Unión con buscadores reusados **7/8 ok**. Firmas A/B del índice = 0 (bean → `Turnos` delegator; SoT a mano: `f_suspender_turnos` · `f_quitar_suspension`).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **TURNOS** (`f_suspender_turnos` · `f_quitar_suspension` · `pp_libera_turno` / `pf_hist_turno` invocados) | **liberado** (gate-done) |
| Rango Flyway | **n/a** (tablas en dump: `turno`, `turno_a_reasignar`, `hist_turno`, `tmp_turno`) | — |
| Tablas `ts` que escribe | `turno` (estado `SUSPENDIDO`/`LIBRE` + split); `turno_a_reasignar` si hay paciente; `hist_turno` vía `pf_hist_turno` | — |
| Rama | `dev/t64-suspender-grilla` (Api/Web/Migration) | **creada** post-FIRME |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · personal 90001 · CC 92001 | no | T1/T2 maestros | vigente |
| Hab + horario + slots `LIBRE` | no | CUs T2/T3/T4 | G6: acto T4 en rango dedicado, **no seed** de `turno` |
| Motivo suspensión | no | T1 GET `motivo` | vigente (ABM motivo fuera) |
| Fila `OTORGADO` para rama cola | no | CU T5 en el mismo rango | si falta → `diferido(fixture)` esa pata |
| Equipo / `cod_item_equipo` | no | [`turnos-grilla-suspender-equipo`](../turnos-grilla-suspender-equipo/) | **gate-done** 2026-09-28 |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** publicado · **gate-done** |
| Reservas | BODY TURNOS **liberado** · Flyway n/a |
| Fixture | T4 `LIBRE` id `17255154`; motivo GET; no seed |
| Evidencia | [verify-report.md](verify-report.md) · 9 filas **verificado** · G6 Francisco |
| Diferidos abiertos | vencidos · T7 · `diferido(auditoria)` · `diferido(perf-volumen)`. Equipo [`turnos-grilla-suspender-equipo`](../turnos-grilla-suspender-equipo/) **gate-done** 2026-09-28 |
| Próximo paso | ninguno de este corte · T6.5 [`turnos-grilla-reemplazo`](../turnos-grilla-reemplazo/) **gate-done** · T6 padre resto: vencidos |

Rutas Web (paridad T4, menú TURNOS / Agenda turnos; URL puede quedar bajo `/configuracion/…`):

- `/configuracion/grilla-turnos-suspender`
- `/configuracion/grilla-turnos-quitar-suspension`

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap (vacío) |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy menú / toast / popup |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Consultar · seleccionar · motivo · quitar |
| [inventario-validaciones.md](inventario-validaciones.md) | BB + `Raise_application_error` contrato |
| [inventario-geometria.md](inventario-geometria.md) | North radio T4 · tabla · popup motivo |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin Flyway |

**No es** reemplazo profesional. **No es** job vencidos. **No es** accordion Agenda.  
**No es** mail/SMS/WhatsApp (T7). **No es** ATENCION `f_suspender_turnos`. El radio Equipo quedó en [`turnos-grilla-suspender-equipo`](../turnos-grilla-suspender-equipo/).
