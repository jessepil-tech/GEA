---
title: Tasks — T2 Turnos habilitación
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-config-hab-horarios
---

# Tasks — T2

1. [x] **TSK-ops-h0-1** Firma Clarify (#1–#4) en [spec.md](spec.md) — **2026-08-27**
2. [x] **TSK-ops-h0-2** Abrir SDD + links relevamiento/cortes/matriz (2026-08-27)
3. [x] **TSK-app-h1-1** Flyway `hab_turnos_serv_centro` / `_pers_serv` / `_equipo_serv` — `V36__ts_turnos_hab.sql`
4. [x] **TSK-app-h2-1** Seed DEV + `checkVigencia` — `V37__seed_turnos_hab.sql` + `TurnosHabPort` / `POST /api/v1/turnos/hab/check`
5. [x] **TSK-app-h3-1** Port + CQRS GET/POST/PUT + Resource (`/api/v1/turnos/hab/{serv,pers,check}`)
6. [x] **TSK-app-h3-2** IT `TurnosHabResourceIT` (list / create / check / 401) — requiere Dev Services o PG de test
7. [x] **TSK-web-h4-1** UI CONFIGURACION: `/configuracion/hab-turnos-serv` + `/hab-turnos-pers` (Buscar → lista → Agregar/Editar/Eliminar); `/turnos/hab` redirige
8. [x] **TSK-ops-h5-1** Smoke + [verify-report.md](verify-report.md) — **PASS 2026-08-27**
9. [x] **TSK-ops-h5-2** Actualizar matriz/maestros/pendientes (`atiende_turnos` diferido D-TUR-11)
