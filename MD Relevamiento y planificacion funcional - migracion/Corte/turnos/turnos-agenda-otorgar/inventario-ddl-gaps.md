---
title: Inventario DDL gaps — T5 agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-otorgar.ddl
---

# Inventario DDL gaps — T5 (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md). Solo `ts.*`.

| Objeto | ¿En Flyway Api? | ¿Bloquea T5 v1? | Acción G1 |
|--------|-----------------|-----------------|-----------|
| `ts.turno` (+ `session_id`, `fecha_session`, estados) | **Sí** V31 | Sí | Usar |
| `ts.hist_turno` | **Sí** V43 | Sí (otorga/libera) | Usar; verificar `next_id` hist |
| `ts.tmp_turno` / `tmp_turno_dia` | No | No | **No crear** — DTO (Clarify C4) |
| `ts.ctrl_turnos_pac` | **No** | Condicional (tope en grilla) | **diferido** `turnos-ctrl-ctd-max-pac` — G1 2026-09-07 |
| `ts.motivo` + tipo motivo liberación | Seed T1 GET | Sí libera | **V46** seed `LIBERACION_TURNO` id 91004 ✅ |
| `ts.pre_agenda_turno` | No | No (hijo) | Fuera T5 |
| `ts.mensaje_turno` | No | No en otorga | T7; libera v1 puede omitir DELETE si tabla ausente |
| FKs `turno`→paciente/convenio | Diferidas P-ORA | No bloquea piloto | Siguen pendientes-solo-oracle |

**Decisión G1 (2026-09-07):** Flyway **V46** = seed `LIBERACION_TURNO` (G4 prep). `ctrl_turnos_pac` → **diferido** `turnos-ctrl-ctd-max-pac` (`cantidadMaxTurnoExcedida` null en grilla v1).
