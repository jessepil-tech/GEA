---
title: Tasks — Turnos hab buscadores
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-hab-buscadores
---

# Tasks — Buscadores hab turnos

1. [x] **TSK-ops-b0-1** Firma Clarify (#1–#3) en [spec.md](spec.md)
2. [x] **TSK-ops-b0-2** Confirmar columnas `servicio_centro` / `personal_servicio` en inventario `ts` (evidencia en spec: 78/21)
3. [x] **TSK-app-b0-1** Flyway DDL + seed DEV (par 1001/10 / personal 90001) — `V38`/`V39`
4. [x] **TSK-app-b1-1** Port + queries + Resource GET búsqueda serv-centro / pers-serv
5. [x] **TSK-app-b1-2** IT list / vacío / 401 (código + smoke API equivalente 401/lista; IT Quarkus DevServices opcional)
6. [x] **TSK-web-b2-1** Dialog buscador (labels i18n, paginator, Aceptar/Cancelar, botones compactos)
7. [x] **TSK-web-b3-1** Integrar en `hab-turnos-serv` y `hab-turnos-pers` (flujo legacy)
8. [x] **TSK-ops-b4-1** Smoke + [verify-report.md](verify-report.md) (Paridad UI) — **PASS 2026-08-28**
9. [x] **TSK-ops-b4-2** Actualizar matriz/maestros T2 verify link + backlog
