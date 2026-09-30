---
title: SDD — T6.5 · reemplazo profesional de grilla
description: >-
  Menú Agenda turnos: reemplazoPersonalGrillaTurnos.
  Port f_reemplazar_personal (reemplazar + quitar en la misma hoja).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo
---

# T6.5 — Reemplazo profesional grilla (`turnos-grilla-reemplazo`)

**Estado:** **gate-done** 2026-09-21 · Clarify **FIRME Camino 1** D-TUR-74 (Francisco).  
Padre T6 `turnos-ciclo-vida` (resto **diferido**: vencidos).  
Hermano T6.4 [`turnos-grilla-suspender/`](../turnos-grilla-suspender/) · T4 [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) (mismo menú `agenda_turnos`).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A8.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Instalación de referencia: Call Center Demo. `BBReemplazoPersonalGrillaTurnos` **sin** `esClienteX()`.

Índice (2026-09-21): `--semilla reemplazoPersonalGrillaTurnos` xhtml **3/8 ok** · beans **2/15 ok** · firmas A/B del índice = 0 (bean → `Turnos` delegator; SoT a mano: `f_reemplazar_personal` · `f_get_grilla_personal_fechas` · `pf_hist_turno`). `--jobs turno`: CheckHabTurnosJob · MailTurnoJob · MigrarTurnoVencidoJob.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **TURNOS** (`f_reemplazar_personal` · `f_get_grilla_personal_fechas` · `pf_hist_turno` invocado) | **liberado** (gate-done) |
| Rango Flyway | **n/a** (tablas en dump: `turno`, `hist_turno`, `tmp_turno`, `motivo`) | — |
| Tablas `ts` que escribe | `turno` (`id_personal_reemplazo` / `id_motivo_reemplazo` / `personal_reemplazado` + split); `hist_turno` vía `pf_hist_turno` | — |
| Rama | `dev/t65-reemplazo-grilla` (Api/Web/Migration) | **creada** post-FIRME |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · personal 90001 · CC 92001 | no | T1/T2 maestros | vigente |
| Hab + horario + slots `LIBRE` | no | CUs T2/T3/T4 | G6: acto T4 en rango dedicado, **no seed** de `turno` |
| Segundo profesional mismo servicio (reemplazante) | no | padres `90002` (`scripts/sql/seeds/padres/…90002_reemplazo.sql`) · T1/T2 | vigente (mock sin grilla; no seed `turno`) |
| Motivo `tipo_motivo=REEMPLAZO_TURNO` activo/seleccionable | no | T1 GET `motivo` | vigente (ABM motivo fuera) |
| Fila `OTORGADO` para rama parcial | no | CU T5 en el mismo rango | si falta → `diferido(fixture)` esa pata |
| Equipo / `cod_item_equipo` | no | D-TUR-17 | **N/A** esta hoja (no hay combo) |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** publicado · **gate-done** |
| Reservas | BODY TURNOS **liberado** · Flyway n/a |
| Universo firmado | inventarios de este slug |
| Fixture | T4/T5 `17255210` OTORGADO · `17255283` LIBRE; padres `90002`; no seed `turno` |
| Evidencia | [verify-report.md](verify-report.md) · 9 filas **verificado** · G6 Francisco |
| Diferidos abiertos | vencidos · T7 · `diferido(auditoria)` · `diferido(perf-volumen)` |
| Próximo paso | ninguno de este corte · T6 [`turnos-ciclo-vida`](../turnos-ciclo-vida/) **gate-done** · T7 [`turnos-avisos`](../turnos-avisos/) **gate-done** |

Ruta Web (paridad T4, menú TURNOS / Agenda turnos; URL puede quedar bajo `/configuracion/…`):

- `/configuracion/grilla-turnos-reemplazo`

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap **PASS** |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy menú / toast / confirm |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Buscar · consultar · reemplazar · quitar |
| [inventario-validaciones.md](inventario-validaciones.md) | BB + `Raise_application_error` contrato |
| [inventario-geometria.md](inventario-geometria.md) | North T4 · tabla · footer v1.19 |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin Flyway |

**No es** suspender/quitar (T6.4). **No es** job vencidos. **No es** accordion Agenda.  
**No es** equipo usable (esta hoja no tiene combo). **No es** mail/SMS (este SP no los escribe).  
**No es** `f_reemplazar_personal_cola` (RECEPCIONES). **No es** ATENCION.
