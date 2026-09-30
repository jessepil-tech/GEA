---
title: Inventario DDL gaps — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-consultas.ddl
---

# Inventario DDL gaps — Consulta Agenda (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
No escanear Oracle para completar el spec.

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| `ts.turno` | **Sí** T4/T5 | No | SELECT rango + joins labels T5 |
| `TS.TURNOS.f_get_turnos_fecha` | **No** (package Oracle) | No | **No clonar.** JDBC equivalente. Gap package → no P-ORA nuevo si el SELECT cubre el CU |
| `ConsultaAgenda.rptdesign` | **Sí** Hospital-Reports (registrado + callable `p_get_turnos_fecha`) | No | **Cable** Api `ReportsPort`; **no** re-migrar diseño. **Filas PDF** → `turnos-agenda-consultas-pdf` |
| Excel POI | No (HIS `generarReporteExcel`) | No | **diferido** `turnos-agenda-consultas-export` |
| Equipo / hab equipo | T2 parcial | No | D-TUR-17 |

G1: **no** ALTER para este corte.
