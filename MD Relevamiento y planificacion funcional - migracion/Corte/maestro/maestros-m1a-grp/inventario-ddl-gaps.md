---
title: Inventario DDL — M1a grp
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.ddl
---

# Inventario DDL — M1a grp

Dump `grupogea-hospital_dev` `ts.grp_centro_atencion` (`information_schema` 2026-09-18):

| Columna | Dump | Uso v1 |
|---------|------|--------|
| `id_grp_centro_ate` | numeric NOT NULL PK | NextId `GRP_CENTRO_ATENCION` |
| `grp_centro_ate` | varchar(45) NOT NULL | nombre |
| `fecha_last_update` | timestamp NULL | `CURRENT_TIMESTAMP` |
| `actualizado_por` | varchar(30) NULL | actor JWT |
| `permite_elegir_centro_turnos` | char(1) NOT NULL | S/N |

Sin `CREATE TABLE`. Sin seed de filas de este ABM. `sec_id_tabla` GRP_CENTRO_ATENCION vía `db/dev-seed/ts_maestros_m1a_grp.sql` si el dump no trae la fila.
