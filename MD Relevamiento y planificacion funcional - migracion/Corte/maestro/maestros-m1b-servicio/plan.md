---
title: Plan — M1b servicio + vínculo
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1b-servicio.plan
---

# Plan — M1b

1. Catálogo `ts.servicio` **primero** (sin padres). Chrome listado HAB (firma 2026-09-17b).
2. G0 copy/validaciones ya en este slug; geometría de campo → dialog.
3. Vínculo `ts.servicio_centro` con id centro M1a vigente (`1002`).
4. UI hoja Servicio y Servicio Centro bajo AG. Tabs amb/int/lab fuera.
5. Sin Flyway `ts.*`. Seed solo `ts.sec_id_tabla` SERVICIO si falta (dev-seed, no Vnn).
