---
title: Inventario DDL — M1c especialidad
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1c-especialidad.ddl
---

# Inventario DDL — M1c

Dump `grupogea-hospital_dev` `ts.especialidad`:

| Columna | Dump | Uso v1 |
|---------|------|--------|
| `id_especialidad` | numeric NOT NULL PK | NextId `ESPECIALIDAD` |
| `especialidad` | varchar(45) NULL | nombre |
| `interconsulta` | char(1) NULL | S/N |
| `adulto_pediatrico` | varchar(15) NULL | ADULTO / PEDIATRICO / TODOS |
| `actualizado_por` | varchar(30) NULL | actor JWT |
| `fecha_last_update` | timestamp NULL | `CURRENT_TIMESTAMP` |

`sec_id_tabla` sin fila ESPECIALIDAD → seed dev (no Flyway). Tabla vacía al abrir el corte.
