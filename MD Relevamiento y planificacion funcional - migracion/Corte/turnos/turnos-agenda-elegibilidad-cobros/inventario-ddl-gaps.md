---
title: Inventario DDL gaps — T5.1c elegibilidad
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros.ddl
---

# Inventario DDL gaps — Elegibilidad (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
No escanear Oracle para completar el spec. Columnas = Reports seed `public.doc_req_*` + V31 convenio/plan.

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| `ts.convenio.req_valid_elegibilidad` | **Sí** V31 | No | Flag `controlElegibilidad` |
| `ts.convenio.id_validador_online` | **Sí** V31 | No | HIS `convenioValidElegibilidad` |
| `ts.convenio.descripcion` | **Sí** V31 | No | obs info convenio (ya) |
| `ts.plan_convenio.observaciones` | **Sí** V31 | No | ya |
| `ts.plan_convenio.mascara_nro_afi` | **Sí** V31 | No | máscara north |
| `ts.validador_online_df` | **Sí** V31 | No v1 seed | `valida_nro_documento`; URLs WS = P-ORA-010 |
| `elegibilidad_seed` | **Sí** V12 (`public`) | No | oráculo v1 (helper AGI; no es tabla TS) |
| `ts.doc_requerido` | **No** (Reports `public`) | **Sí** filas | **G1** este slice |
| `ts.doc_req_plan_conv` | **No** | **Sí** | **G1** |
| `ts.doc_req_prest_plan` | **No** | **Sí** si hay prestación | **G1** |

G1: portar DDL a `ts` (paridad nombres Oracle TS; tipos PG del catálogo). No dejar el GET leyendo `public.doc_req_*`.

P-ORA-010: tabla config **sí**; clientes WS **no**. No se cierra en este corte.
