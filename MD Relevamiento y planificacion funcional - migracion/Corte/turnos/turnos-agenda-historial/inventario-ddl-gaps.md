---
title: Inventario DDL gaps — T6.3 historial turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.ddl
---

# Inventario DDL gaps — Historial Turnos

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

No `Vnn` para tablas del dump. Este corte **no** siembra `hist_turno`.

| Objeto | ¿En dump `ts`? | ¿Bloquea v1? | Acción |
|--------|----------------|--------------|--------|
| `ts.hist_turno` | Sí (V43) | No | SELECT lista + detalle |
| Labels centro/servicio/personal/prestación | Sí T1–T5 | No | JOIN enrich (hbm formulas) |
| `ts.te_persona` teléfono | Sí | No | puede ir NULL; no portar `f_get_persona_telefono` |
| Package TURNOS / PERSONAS | dump | No | no clonar; JDBC **rediseñar** |

G1: **no** ALTER. Rareza UI: `estado_turno='SUSPENDIDO'` se pinta `CANCELADO`.
