---
title: Plan — M1a logos centro
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro-west.plan
---

# Plan — M1a logos

1. G0 copy/validaciones/geometría (este slug) **antes** de scaffold Web.
2. API CQRS + JDBC sobre dump `ts.pack_logos`. Seed `sec_id_tabla` PACK_LOGOS (dev-seed, no Vnn). No INSERT de filas de pack.
3. UI: acción Logos en listado HAB centros (10203) + dialog 7 slots. Clone PUT binario anunciador.
4. Recarga: overlay `LoadingInterceptor`.
5. Evidencia PUT CU en dump (`id_pack_logos` + `centro_atencion.id_pack_logos`). Sin Flyway `ts.*`.
