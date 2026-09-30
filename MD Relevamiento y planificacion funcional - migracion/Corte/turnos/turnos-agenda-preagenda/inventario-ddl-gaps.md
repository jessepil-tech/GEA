---
title: Inventario DDL gaps — T5.6 pre-agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-preagenda.ddl
---

# Inventario DDL gaps — Pre-agenda (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
No escanear Oracle para completar el spec.

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| `ts.pre_agenda_turno` | **No** (secuencia V10 sí) | Sí para lista vacía CI | G1 `CREATE IF NOT EXISTS` columnas entity: `id_pre_agenda_turno`, `cod_prestacion`, `id_prestacion`, `id_servicio`, `id_paciente`, `fecha_prescrip`, `id_det_prescrip_prest_amb`, `id_det_prescrip_prest_int`, `id_turno`, `estado_pre_agenda`, `fecha_cancela`, `id_personal_cancela`, `id_motivo_cancela` (+ audit si el dump las tiene). Dump piloto **ya** tiene la tabla. |
| `sec_id_pre_agenda_turno` | **Sí** V10 | No | No recrear |
| `ts.paciente` / `servicio` / `prestacion` / `convenio` / `plan_convenio` | Sí T1–T5 | No | JOIN labels |
| `ts.atencion_amb` / `prescrip_prest_amb` / `det_prescrip_prest_amb` (y rama int) | **No** Flyway | No lista; sí filtro convenio | LEFT EXISTS en dump; CI sin convenio |
| Package ATENCION insert | **No** | No | Fuera (alta) |
| `TS.PERSONAS.f_get_persona_telefono` | **No** | No | JDBC equivalente teléfonos (mismo patrón T5 ficha) o subquery `telefono_persona` |

G1: **sí** Flyway tabla (IF NOT EXISTS). Un writer `Vnn`.
