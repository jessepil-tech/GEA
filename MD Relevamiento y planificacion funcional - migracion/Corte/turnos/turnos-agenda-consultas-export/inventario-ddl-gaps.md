---
title: Inventario DDL gaps — T5.5 hijo Excel
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export.ddl
---

# Inventario DDL gaps — Excel Consulta Agenda

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| `ts.turno` | Sí T4/T5 | No | SELECT T5.5 |
| `ts.paciente.nro_hc_anterior` | Sí V31 | No | SELECT extra |
| `ts.te_persona` | Sí V56 (vacía) | No | tel puede ir NULL |
| `mail_persona` | **No** | No | columna Excel NULL |
| Package TURNOS | No | No | no clonar |

G1: **no** ALTER.
