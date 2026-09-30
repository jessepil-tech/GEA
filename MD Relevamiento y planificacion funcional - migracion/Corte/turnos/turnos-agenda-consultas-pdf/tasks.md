---
title: Tasks — T5.5 hijo · PDF consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-pdf.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 — 2026-09-15 (Francisco: Flyway + params; registrar SDD)
2. [x] **TSK-ops-g0-2** Inventario DDL gaps + spec/plan/verify — 2026-09-15
3. [x] **TSK-app-g1-1** Flyway `V56` `ts.turno_vencido` + `ts.equipo_serv_centro` + `ts.te_persona` IF NOT EXISTS — aplicado en dump 2026-09-15
4. [x] **TSK-rep-g2-1** `p_get_turnos_fecha`: `NULLIF(id, 0)`; reinstall piloto 2026-09-15
5. [x] **TSK-rep-g2-2** Quitar default `44983` en `ConsultaAgenda.rptdesign`; imagen `hospital-reports:dev-local` rebuild (health UP)
6. [x] **TSK-app-g2-3** Api omite ids Todos/`0`; `GetConsultaAgendaPdfQueryHandlerTest` 4 tests PASS (`mvn -pl application -am`)
7. [x] **TSK-ops-g3-1** SQL dump: `turno_hoy=2` = `nulls` = `zeros` = `1001_and_zeros`; `birt_44983=0`
8. [x] **TSK-ops-g6-1** Smoke Francisco: Imprimir PDF con información — 2026-09-15 «ok ahora se está visualizando información en los pdf»
9. [x] **TSK-ops-g6-2** Verify PASS + gobierno (ledger G6 verificado)

## Gate UI

| Path | Rol | DoD |
|------|-----|-----|
| `consulta.xhtml` L217–219 | Imprimir | **N/A este hijo** — chrome T5.5; acá contenido PDF |

**Prohibido:** re-portar diseño; job vencidos; Excel; habilitar combo equipo.
