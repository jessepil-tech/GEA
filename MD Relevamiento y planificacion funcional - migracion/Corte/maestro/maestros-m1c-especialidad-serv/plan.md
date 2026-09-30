---
title: Plan — especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.plan
---

# Plan — especialidad_serv

1. G0 inventarios (este slug) antes de template.
2. API CQRS `ts.especialidad_serv` (JDBC, sin Flyway, sin NextId).
3. Listado HAB + dialog 600px. Filtros q / centro / servicio.
4. Padres = GET M1a/M1b/M1c. No seedear esta tabla.
5. Evidencia dump del triple; Playwright diferido.
