---
title: Inventario DDL gaps — turnos múltiples
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-multiples.ddl
---

# Inventario DDL gaps

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.turno` | Sí V31 | No | UPDATE reserva N (T5) |
| `ts.tmp_turno` / `tmp_turno_filtro` | **No** | No | **No crear** — filtros JSON / DTO (D-TUR-55) |
| Package `f_get_dias_disp_multiple` / `f_get_grilla_multiple_dia` / `f_reserva_turno_multiple_pac` | N/A (Oracle) | Sí lógica | Port CQRS/JDBC; **no** PL/pgSQL |

G1: sin ALTER. Sin `public` ni `*_agi`.
