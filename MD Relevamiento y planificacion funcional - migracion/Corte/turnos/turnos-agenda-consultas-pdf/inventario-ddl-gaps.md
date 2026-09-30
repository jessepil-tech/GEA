---
title: Inventario DDL gaps — T5.5 hijo PDF consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-pdf.ddl
---

# Inventario DDL gaps — PDF Consulta Agenda (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
DoR del cable: [`regla-migracion-reportes-birt.md`](../../../canon/regla-migracion-reportes-birt.md) § Restricciones punto 5 (este corte cruza el print-path vs **esta** PG).

| Objeto | ¿En Flyway Api? | ¿En dump piloto 2026-09-15? | ¿Bloquea v1? | Acción |
|--------|-----------------|-----------------------------|--------------|--------|
| `ts.turno` | Sí V31 | Sí (2 filas hoy) | No | Grilla T5.5; rama vigente del package |
| `turnos.p_get_turnos_fecha` | N/A (Reports packages-pg) | Sí (`NULLIF` 0 reinstall 2026-09-15) | No | Defensa `0`=Todos |
| `ts.turno_vencido` | **V56** | Sí (vacía) | UNION desbloqueado | G1 hecho |
| `ts.equipo_serv_centro` | **V56** | Sí (vacía) | subquery OK | G1; D-TUR-17 usable diferido |
| `ts.te_persona` | **V56** | Sí (vacía) | nro_tel_pac | fn califica `ts.te_persona` |
| `ts.tmp_turno` | No | No | No | Diferido CHECKLIST (LIBRE); print no la usa |
| `ts.centro_atencion` / `servicio` / `persona` / `prestacion` / `convenio` / `plan_convenio` / `paciente` | Sí | Sí | No | LEFT JOIN labels |
| `personas.f_get_persona_telefono` | Reports | Sí (reinstall 2026-09-15) | No | `ts.te_persona`; null id → NULL |
| Job `f_migra_turno_vencido` | No | No | No | T6 |

G1: **sí** Flyway (IF NOT EXISTS). Un writer `V56`.
