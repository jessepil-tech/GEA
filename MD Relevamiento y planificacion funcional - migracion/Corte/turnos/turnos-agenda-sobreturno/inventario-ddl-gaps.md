---
title: Inventario DDL gaps — T5.2 sobreturno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno.ddl
---

# Inventario DDL gaps — Sobreturno (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
No escanear Oracle para completar el spec.

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| `ts.turno.sobreturno` | **Sí** V31 | No | INSERT `'S'` (API T5) |
| `ts.turno.id_motivo_sobreturno` | **Sí** V31 | No | nullable; seed motivo `91002` |
| `ts.motivo` tipo `SOBRETURNO` | **Sí** V35 | No | `GET /api/v1/turnos/motivos?tipoMotivo=SOBRETURNO` |
| `ts.ctrl_turnos_pac` tope SOBRETURNOS | **No** | No v1 | **diferido** `turnos-ctrl-ctd-max-pac` |
| Equipo / `hab_turnos_equipo_*` | T2 parcial | No | **diferido** D-TUR-17 |
| Grilla del día (`ts.turno` LIBRE T4) | **Sí** | No v1 | Adapter T5 no exige oferta; GET grilla solo para `$popUpInfoTurnosDeHoy` |

G1: **no** ALTER para este slice.
