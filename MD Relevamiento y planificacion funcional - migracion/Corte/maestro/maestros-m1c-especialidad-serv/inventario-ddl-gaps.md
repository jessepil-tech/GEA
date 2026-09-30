---
title: Inventario DDL — especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.ddl
---

# Inventario DDL — G0

Dump `ts.especialidad_serv` (sin Flyway):

| Columna | Dump | Uso v1 |
|---------|------|--------|
| `id_centro_ate` | numeric NOT NULL | PK |
| `id_servicio` | numeric NOT NULL | PK |
| `id_especialidad` | numeric NOT NULL | PK |
| `activo` | char(1) | S/N |
| `nro_orden` | numeric | default 1 |
| `cerrar_ate_amb_sin_diag` | char(1) | S/N |
| `atencion_enfermeria` | char(1) | S/N |
| `modalidad` | varchar(2) | hisName |
| `mensaje_espera_serv_centro` | varchar(60) | hisName |
| `ctd_etiquetas_modalidad` | numeric | default 0 |
| `permite_internacion` | char(1) | S/N |
| `tipo_internacion` | varchar(60) | HOSPITALARIA / TRANSITORIA / AMBAS |
| `actualizado_por` | varchar(30) | JWT |
| `fecha_last_update` | timestamp | `CURRENT_TIMESTAMP` |

Nombres de especialidad/servicio/centro = JOIN lectura, no columnas.
