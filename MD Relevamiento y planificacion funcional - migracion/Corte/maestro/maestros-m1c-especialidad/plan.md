---
title: Plan — M1c especialidad
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1c-especialidad.plan
---

# Plan — M1c

1. G0 copy/validaciones/geometría (este slug) **antes** de scaffold Web.
2. API CQRS + JDBC sobre dump `ts.especialidad`. Seed `sec_id_tabla` ESPECIALIDAD (dev-seed, no Vnn).
3. UI listado HAB bajo AG · Configuración General, mismo chrome que Servicio.
4. Recarga: overlay `LoadingInterceptor`, no `Cargando…` en el form.
5. Evidencia INSERT CU en dump. Sin Flyway `ts.*`.
