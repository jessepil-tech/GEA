---
title: Inventario gaps DDL — T4 generación grilla
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-generacion-grilla.ddl-gaps
---

# Inventario gaps DDL — T4 (G1 aplicado)

Canon: schema `ts`. Flyway: `V43` · `V44` · `V45` (drop FK hist→turno para G3).

| Objeto | Estado post-G1 | Nota |
|--------|----------------|------|
| `ts.fecha_feriado` | **done** V43 + seed V44 (93001/93002) | `sec_id_fecha_feriado` |
| `ts.eliminacion_agenda_turnos` | **done** V43 | `sec_id_eliminacion_agenda_turnos` |
| `ts.turno_a_reasignar` | **done** V43 | seq V10 + registry |
| `ts.hist_turno` | **done** V43 (57 cols) | FK → `turno`; seq V10 |
| FK `hist_turno` → `turno` | **dropped** V45 | auditoría hist con id huérfano (paridad SP) |
| `tmp_turno` / `tmp_observaciones` | N/A | API lista ids / observaciones |
| FKs personal/paciente en `turno` | diferido | P-ORA |

## NextId registry

Agregados: `FECHA_FERIADO`, `ELIMINACION_AGENDA_TURNOS` (main + golden registry).

## Notas

- Reiniciar Api para aplicar V43/V44.
- Smoke T4: rango fecha dedicado; no chocar seed OTORGADO.
- Horario especial tablas: **no** G1 (D-TUR-15).
