---
title: Inventario DDL gaps — T6 migrar turno vencido
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-ciclo-vida.ddl
---

# Inventario DDL gaps — Migrar turno vencido

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md). **Sin Flyway** en este corte.

| Objeto | ¿En dump / Flyway? | ¿Este corte escribe? | Acción |
|--------|--------------------|----------------------|--------|
| `ts.turno` | Sí V31 | **Sí** DELETE | G1 n/a |
| `ts.turno_vencido` | Sí (T5.5-pdf vacía) | **Sí** INSERT (mismo id) | G1 n/a |
| `ts.mensaje_turno` | dump | **Sí** DELETE tras copia | G1 n/a |
| `ts.mensaje_turno_vencido` | dump | **Sí** copia | G1 n/a |
| `ts.cola_espera_recep` / `cola_espera_triage` | dump | **Sí** UPDATE 6 h | G1 n/a |
| `ts.cola_espera_serv_amb` / `det_indica_prest_int` / `atencion_int` / `sms_recibido` | dump | **Sí** unlink | G1 n/a |
| `ts.llamado_anunciador` | dump | **No** (expire ya cobrado) | no tocar |
| `ts.AUD_TURNO` | dump | **No** | **diferido(auditoria)** |
| `ts.tarea_programada` | dump | **No** | **diferido(disparador)** |
