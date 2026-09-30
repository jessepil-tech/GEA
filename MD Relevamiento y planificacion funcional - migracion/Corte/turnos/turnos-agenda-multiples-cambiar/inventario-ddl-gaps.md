---
title: Inventario DDL gaps — cambiar horario turnos múltiples
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.ddl
---

# Inventario DDL gaps

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.turno` | Sí V31 | No | Lectura GET T5; escritura **no** en este slice (Asignar = padre) |
| `ts.tmp_turno` / `tmp_turno_filtro` | **No** | No | **No crear** — HIS usa `id_motivo_suspende` = `id_objeto` filtro; Web usa keys de la fila |

Sin CREATE. Sin `public` ni `*_agi`. G1 Flyway **N/A**.
