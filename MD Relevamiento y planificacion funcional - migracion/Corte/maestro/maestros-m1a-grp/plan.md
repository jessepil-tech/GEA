---
title: Plan — M1a grp
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.plan
---

# Plan — M1a grp

1. G0 copy/validaciones/geometría (este slug) **antes** de scaffold Web.
2. API CQRS + JDBC sobre dump `ts.grp_centro_atencion`. Seed `sec_id_tabla` GRP_CENTRO_ATENCION (dev-seed, no Vnn). No INSERT de filas de catálogo.
3. UI listado HAB bajo AG · Configuración Operativa · Centro Atención (L3).
4. Recarga: overlay `LoadingInterceptor`.
5. Evidencia INSERT CU en dump. Sin Flyway `ts.*`.
