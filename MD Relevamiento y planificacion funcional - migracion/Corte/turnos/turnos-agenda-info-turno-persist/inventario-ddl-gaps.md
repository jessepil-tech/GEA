---
title: Inventario DDL gaps — persist infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.ddl
---

# Inventario DDL gaps

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.turno.observaciones` varchar(250) | V31 | No | UPDATE al otorgar |
| `ts.turno.fecha_prescripcion` timestamp | V31 | No | UPDATE al otorgar |
| `ts.servicio_centro.req_fecha_prescrip_amb` | V38 | No | leer al validar |
| `ts.plan_convenio.req_ctrl_fecha_prescrip` | V31 | No | leer al validar |
| `ts.plan_convenio.ctd_max_dias_prescrip` | V31 | No | leer al validar |

G1: **no** ALTER.
