---
title: Inventario DDL gaps — T5.1d cobros
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-cobros.ddl
---

# Inventario DDL gaps — Cobros (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).  
No escanear Oracle para completar el spec.

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| HIS `ParamGeneral.id_convenio_dflt` / plan dflt | **No** `ts.param_general` | No (v1) | **D-TUR-43** — `ts.call_center.id_convenio_dflt` (V50 → 5100) + `convenio.id_plan_conv_dflt_tur` (51001) |
| `ts.call_center.id_convenio_dflt` | **Sí** V34 + V50 | No | Seed demo 5100 |
| `ts.convenio` / `ts.plan_convenio` | **Sí** V31 | No | destino del swap |
| Saldo CTA paciente | **No** en corte agenda | Display | seed/lectura mínima o fila vacía HIS |
| Montos coseguro en `ts.turno` | verificar V31 | Display | null = no render (paridad `rendered`) |
| `elegibilidad_seed` | **Sí** V12 | No | dispara rechazo T5.1c |

P-ORA-010: **no** se cierra. Facturación: **no** portar packages.
