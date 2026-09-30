---
title: SDD — T6 · migrar turno vencido
description: >-
  Port f_migra_turno_vencido (archivo de oferta vieja + purga cola 6 h).
  Disparador en Hospital-Api; no clonar el WAR SCHEDULER.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-ciclo-vida
---

# T6 — Migrar turno vencido (`turnos-ciclo-vida`)

**Estado:** **gate-done** 2026-09-21 · Clarify **FIRME Camino 1** D-TUR-75 (Francisco «ok»).  
Resto T6.1–T6.5 **gate-done**. Este slug cobra el job que el plan origin dejó en T6.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A8.  
Jobs: [`relevamiento-procesos-programados/`](../../../relevamiento/relevamiento-procesos-programados/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Instalación de referencia: Call Center Demo. `f_migra_turno_vencido` **sin** `esClienteX()`.

Índice (2026-09-21): `--jobs turno` → `CheckHabTurnosJob` · `MailTurnoJob` · `MigrarTurnoVencidoJob`. Semilla = job (no `ACCION` de menú).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **TURNOS** (`f_migra_turno_vencido`) | **liberado** (gate-done) |
| Rango Flyway | **n/a** (tablas en dump: `turno`, `turno_vencido`, `mensaje_turno`, `mensaje_turno_vencido`, colas) | — |
| Tablas `ts` que escribe | `turno` (DELETE); `turno_vencido` (INSERT); `mensaje_turno` (DELETE); `mensaje_turno_vencido` (copia); `cola_espera_recep` / `cola_espera_triage` (UPDATE 6 h); unlinks `cola_espera_serv_amb` · `det_indica_prest_int` · `atencion_int` · `sms_recibido` | — |
| Rama | `dev/t6-turno-vencido` (Api + Migration; Web **n/a**) | **después de FIRME** |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · personal 90001 · CC 92001 | no | T1/T2 | vigente |
| Slot con `fecha_hora_tur_ini` **< trunc(hoy)** LIBRE | no | SQL G6 `id_turno=19999001` (T4 no acepta fecha pasada) | **hecho** 2026-09-21 · archivado a `turno_vencido` |
| Slot OTORGADO pasado | no | acto T5 sobre el LIBRE anterior | misma regla |
| `turno_vencido` / `mensaje_turno_vencido` | **sí** (este CU) | el job | no seed |
| Equipamiento | no | D-TUR-17 | **N/A** (el cursor copia `cod_item_equipo` si viene) |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · push pendiente (no pedido) |
| Reservas | BODY TURNOS **liberado** · Flyway n/a · rama `dev/t6-turno-vencido` |
| Universo firmado | Camino 1 (inventarios de este slug) |
| Fixture | padres T1/T2 vigentes; LIBRE ayer `19999001` archivado; sentinel hoy `17255155` intacto |
| Evidencia | [verify-report.md](verify-report.md) · 8/8 **verificado** |
| Diferidos abiertos | `diferido(disparador)` `tarea_programada` · `diferido(auditoria)` · `diferido(perf-volumen)` · Kern · `alter system disconnect` |
| Próximo paso | ninguno de este corte · T7 [`turnos-avisos/`](../turnos-avisos/) **gate-done** |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 (sin hoja Web) |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger 8/8 **verificado** |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin Flyway |

**No es** T6.1–T6.5 (ya cobrados). **No es** T7 (`MailTurnoJob` envía). **No es** `CheckHabTurnosJob`.  
**No es** clonar el WAR `SCHEDULER`. **No es** ABM `equipo_tarea_programada`.  
**No es** matar sesiones Oracle.
