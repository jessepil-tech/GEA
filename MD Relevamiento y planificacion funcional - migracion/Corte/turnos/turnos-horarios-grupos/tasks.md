---
title: Tasks — T3 Turnos grupos + horarios
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-horarios-grupos
---

# Tasks — T3

1. [x] **TSK-ops-g0-1** Firma Clarify (#1–#5, #7 + C2a) en [spec.md](spec.md) — **2026-08-28**
2. [x] **TSK-ops-g0-2** Abrir SDD + links relevamiento/cortes/backlog — **2026-08-28**
3. [x] **TSK-app-g1-1** Flyway cadena pers+serv (+ equipo DDL C2a) — `V41__ts_turnos_horarios_grupos.sql`
4. [x] **TSK-app-g1-2** Seed DEV alineado hab T2 + prestación — `V42__seed_turnos_horarios_grupos.sql`
5. [x] **TSK-app-g2-1** Port + CQRS + Resource cadena **pers** + IT — `TurnosHorariosResource` / `TurnosHorariosResourceIT`
6. [x] **TSK-app-g3-1** Port + CQRS + Resource cadena **serv** + IT — mismo Resource `/serv/...`
7. [x] **TSK-web-g4-1** UI TURNOS (menú) pers+serv — rutas `/configuracion/horarios-turnos-pers` · `…-serv` (URL como hab; padre menú ≠ carpeta)
7b. [x] **TSK-web-g4-2** Paridad copy: inventario `msg.*` → labels — [`inventario-copy-msg.md`](inventario-copy-msg.md) **2026-08-28**
7c. [x] **TSK-web-g4-3** Validaciones MessageBundle / BB / ImpBus — UI + API [`inventario-validaciones.md`](inventario-validaciones.md) **2026-08-28** (solapes vigencia/día + copiar valores vigentes)
8. [x] **TSK-ops-g5-1** Smoke + [verify-report.md](verify-report.md) — **PASS 2026-09-01** (V41/V42 en runtime)
9. [x] **TSK-ops-g5-2** Actualizar matriz/maestros/pipeline/backlog + hijos diferidos — **2026-09-01**

### G4+ — buscador prestaciones multi-select (2026-09-01)

- **done** API `GET /api/v1/turnos/buscadores/prestaciones` (`ts.prestacion`, q + soloHabilitadas).
- **done** Web dialog multi-select + alta batch en Turnos por Servicio / por Profesional (paridad `buscadorPrestacion.xhtml` `seleccionMultiple`).
- Edición de prestación asociada permanece single-row (sin buscador).
- Incluido en smoke G5 (paso buscador + UI ops).
