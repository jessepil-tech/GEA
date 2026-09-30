---
title: Inventario DDL gaps — T6.1 reasignar
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-reasignar.ddl
---

# Inventario DDL gaps — Reasignar (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
No escanear Oracle para completar el spec.

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| Estado UI `PENDIENTE_LIBERAR` | **No** (no es columna `ts.turno`) | No | **D-TUR-44** — overlay Web; origen permanece `OTORGADO` hasta cierre |
| `ts.turno` estados reales | **Sí** V31 (`LIBRE`/`RESERVADO`/`OTORGADO`/…) | No | Cierre: nuevo slot `OTORGADO`; origen `LIBRE` (libera T5) |
| `ts.turno_a_reasignar` | **Sí** V43 (cola T4 eliminar) | No | **Fuera** — no es este CU |
| `ts.hist_turno` | **Sí** V43 | No | Reusar T5 insert; origen T6.1 estado `REASIGNADO` (`pf_hist_turno` `'S'`). Pantalla hist → T6 |
| Sesión `TurnoAReasignar` | N/A (Faces session) | No | Sesión Web / payload otorga |

G1: **no** ALTER de `ts.turno` para este slice.
