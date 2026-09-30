---
title: Inventario DDL gaps — T6.5 reemplazo profesional
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.ddl
---

# Inventario DDL gaps — Reemplazo profesional

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md). **Sin Flyway** en este corte.

| Objeto | ¿En dump / Flyway? | ¿Este corte escribe? | Acción |
|--------|--------------------|----------------------|--------|
| `ts.turno` | Sí V31 | **Sí** reemplazo / split | G1 n/a |
| `ts.hist_turno` | Sí | **Sí** `pf_hist_turno` | G1 n/a |
| `ts.tmp_turno` | T4 diferido CHECKLIST | **No** (ids en comando) | no crear |
| `ts.motivo` | T1 GET | No (lee `REEMPLAZO_TURNO`) | no ABM |
| `hab_turnos_*` | T2 | No | gate previa |
| `ts.AUD_TURNO` | dump | **No** (TBL_AUD_*) | **diferido(auditoria)** |
