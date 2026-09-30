---
title: Tasks — T1 Turnos maestros identidad
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-maestros-personal
---

# Tasks — T1

1. [x] **TSK-ops-1** Spec + plan + tasks (este slice)
2. [x] **TSK-app-1** Flyway `ts.personal`, `call_center`, `personal_call_center`, `motivo`, `tipo_motivo_df`, `personal_adm_tur_serv_centro` — `V34__ts_turnos_maestros_personal.sql` (columnas = `pg_ts_columns.csv`)
3. [x] **TSK-app-2** Seed DEV `V35` + `db/dev-seed/ts_turnos_maestros_personal.sql`; FKs `ts.turno` → personal/call_center/motivo **siguen diferidas** (RF-3)
4. [x] **TSK-app-3** `TurnosIdentidadPort` + queries `GetTurnosInicio` / `ListMotivos` + `GET /api/v1/turnos/inicio|motivos`
5. [x] **TSK-app-4** IT `TurnosIdentidadResourceIT` (con vínculo / sin CC / sin personal / 401) — **compilado**; ejecución Dev Services requiere Docker
6. [x] **TSK-web-1** Picker `/turnos/inicio` + tile menú `ATENCION_TURNOS`
7. [x] **TSK-ops-2** `verify-report.md` (smoke host: aplicar Flyway al levantar Api)
8. [x] **TSK-ops-3** Matriz relevamiento-turnos + `pendientes-solo-oracle.md` (nota T1 DDL; FKs turno diferidas)
