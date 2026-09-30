---
title: Tasks — prep / requisitos realización infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** (Francisco «ok firme» 2026-09-10)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-10
3. [x] **TSK-app-g1-1** Flyway: `ts.req_realizacion` + `ts.preparacion_prest` + `ts.req_realiza_prest` + seed CONS/`80001`
4. [x] **TSK-app-g2-1** API GET prep+req (Resource delgado, CQRS, JDBC `ts`)
5. [x] **TSK-app-g2-2** Edad HIS + primera fila prep; req JOIN maestro
6. [x] **TSK-web-g3-0** Gate UI: rellenar paneles existentes **antes** de smoke (no nuevo dialog)
7. [x] **TSK-web-g4-1** Web: al abrir infoTurno GET + HTML prep + tabla req
8. [x] **TSK-web-e2e** Viaje prep+req con fixture CONS; emptyMessage sin datos
9. [x] **TSK-ops-g6-1** Smoke Francisco — **PASS** 2026-09-10 («se ve bien»)
10. [x] **TSK-ops-g6-2** Verify PASS + gobierno

## Gate UI (G3) — xhtml

Chrome **ya** T5.1e. Este corte no cambia geometría.

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `infoTurno.xhtml` L150–153 | Panel Preparación Previa; `outputText escape=false` | 450×260 call center (228 + print **N/A**) |
| `infoTurno.xhtml` L220–232 | Tabla requisitos Documentación / Observaciones | 360×202; emptyMessage |
| Print L154–158 | Solo recepción | **N/A** agenda |

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Abrir infoTurno CONS → prep HTML + fila req | e2e-migrado |
| Prestación sin maestros → emptyMessage | e2e-migrado |
| Legacy HIS | no |
