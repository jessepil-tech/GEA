---
title: Inventario DDL gaps — T6.4 suspender grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.ddl
---

# Inventario DDL gaps — Suspender / quitar suspensión

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md). **Sin Flyway** en este corte.

| Objeto | ¿En dump / Flyway? | ¿Este corte escribe? | Acción |
|--------|--------------------|----------------------|--------|
| `ts.turno` | Sí V31 | **Sí** estado / split | G1 n/a |
| `ts.turno_a_reasignar` | Sí T4 | **Sí** si paciente | G1 n/a |
| `ts.hist_turno` | Sí | **Sí** `pf_hist_turno` | G1 n/a |
| `ts.tmp_turno` | T4 diferido CHECKLIST | **No** (ids en comando) | no crear |
| `ts.motivo` | T1 GET | No (lee) | no ABM |
| `ts.envio_sms` / `mensaje_whatsapp` / `mensaje_turno` | dump | **No** (T7) | diferido |
| `hab_turnos_*` | T2 | No | gate previa |
| `ts.suspension_agenda_turnos` | dump (tabla) | **Sí** batch footer SP | G1 n/a · no seed de la entidad |
| `ts.sec_id_tabla` fila `SUSPENSION_AGENDA_TURNOS` | **hueco dump** (igual T4 `ELIMINACION_AGENDA_TURNOS`) | No (numerador) | pack `scripts/sql/seeds/sec-id/suspension_agenda_turnos.sql` |
| `ts.AUD_TURNO` | dump | **No** (TBL_AUD_*) | **diferido(auditoria)** |
