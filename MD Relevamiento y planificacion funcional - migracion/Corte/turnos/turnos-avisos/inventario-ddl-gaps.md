---
title: Inventario DDL gaps — T7 avisos de turno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-avisos.ddl
---

# Inventario DDL gaps — Avisos de turno

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md). **Sin Flyway** en este corte.

| Objeto | ¿En dump / Flyway? | ¿Este corte escribe? | Acción |
|--------|--------------------|----------------------|--------|
| `ts.mensaje_turno` | dump | **Sí** (UPDATE despacho / housekeeping; raro: nace en vivo si el turno aún no archivó) | G1 n/a |
| `ts.mensaje_turno_vencido` | dump | **Sí** (nace `REPROGRAMACION` al archivar + UPDATE despacho) | G1 n/a |
| `ts.turno` / `ts.turno_vencido` | dump | **No** (T6 ya archiva; este corte lee y engancha el mismo TX) | no duplicar DELETE |
| `ts.centro_atencion` (`id_server_mail` / `id_server_sms` / `cuerpo_mail_reasigna_turno` / `sms_reasigna_turno`) | dump | **No** (padres; Demo null) | config opcional G6, no seed de `mensaje_*` |
| `ts.hab_turnos_pers_serv` (`envia_mail_reasigna_turno` / `envia_sms_turno`) | dump | **No** | mismo |
| `ts.mail_persona` | dump | **No** (lectura plantilla) | — |
| `ts.AUD_*` de mensaje | dump | **No** | **diferido(auditoria)** si aplica |
| `ts.tarea_programada` | dump | **No** | **diferido(disparador)** |
