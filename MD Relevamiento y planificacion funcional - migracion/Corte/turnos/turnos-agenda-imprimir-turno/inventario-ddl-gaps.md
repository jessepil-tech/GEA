---
title: Inventario DDL gaps — T5 hijo PDF turno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-agenda-imprimir-turno.ddl
---

# Inventario DDL gaps — PDF turno Agenda

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md). **Sin Flyway** en este corte.

| Objeto | ¿En dump / Flyway? | ¿Este corte escribe? | Acción |
|--------|--------------------|----------------------|--------|
| `ts.turno` | dump | **No** (lee id/paciente/estado) | — |
| `ts.paciente` / `ts.persona` | dump | **No** (lee `fecha_nacimiento`) | join ficha T5.1 |
| `ts.tmp_recepcion_amb*` | dump | **No** | solo si se porta `f_imprime` (hijo) |
| Datasets BIRT `Turno` | Reports | **No** | print-path ya sidecar |
