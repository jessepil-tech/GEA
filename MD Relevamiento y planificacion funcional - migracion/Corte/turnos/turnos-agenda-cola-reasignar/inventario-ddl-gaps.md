---
title: Inventario DDL gaps — T6.2 cola reasignar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.ddl
---

# Inventario DDL gaps — Cola Reasignación de Turnos

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

Canon Flyway 2026-09-16: no `Vnn` para tablas del dump; seeds = `scripts/sql/seeds/` (este corte **no** siembra la cola).

| Objeto | ¿En dump `ts`? | ¿Bloquea v1? | Acción |
|--------|----------------|--------------|--------|
| `ts.turno_a_reasignar` | Sí (V43 ya aplicada en piloto) | No | SELECT lista · UPDATE obs · DELETE al otorgar |
| Labels centro/servicio/personal/prestación/convenio/plan | Sí T1–T5 | No | JOIN enrich |
| `ts.te_persona` teléfono | Sí V56 | No | puede ir NULL |
| Package TURNOS | No | No | no clonar; JDBC **rediseñar** |
| GTT `ts.tmp_turno` | Oracle only | No | no portar |

G1: **no** ALTER. Rareza: el cursor HIS expone `id_turno_a_reasignar` como `id_turno` / `id_objeto`.
