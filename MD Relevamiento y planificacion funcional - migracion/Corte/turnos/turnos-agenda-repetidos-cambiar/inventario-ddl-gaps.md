---
title: Inventario DDL gaps — cambiar horario repetidos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.ddl
---

# Inventario DDL gaps

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.turno` | Sí V31 | No | UPDATE via POST T5 reservar / liberar |
| Calendario / `fechasHabilitadas` | N/A | No | **WAIVE** (xhtml comentado) |

Sin CREATE. Sin `public` ni `*_agi`. G1 Flyway **N/A**.
