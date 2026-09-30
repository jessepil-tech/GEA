---
title: Inventario DDL gaps — turnos repetidos
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-repetidos.ddl
---

# Inventario DDL gaps

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.turno` | Sí V31 | No | UPDATE reserva N (T5) |
| `ts.tmp_observ_tur_rep` (`id_objeto`, `observacion` varchar 4000, `fecha_hora_ini_turno`) | **No** | No | **DTO** en POST reserva (no CREATE) |
| Package `f_reservar_turnos_repetidos` | N/A (Oracle) | Sí lógica | Port CQRS; **no** PL/pgSQL |

G1: observaciones en el result set. Sin `public` ni `*_agi`.
