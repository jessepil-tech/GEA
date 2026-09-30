---
title: Tasks — T5.5 hijo · Excel consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 — 2026-09-16 (Francisco: arrancar Excel)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify
3. [x] **TSK-app-g2-1** `ExcelExportPort` + POI HSSF infrastructure + GET `exportar.xls`
4. [x] **TSK-app-g2-2** JDBC consulta: `nro_hc_anterior` / tel `te_persona` / mail NULL; extra fields DTO
5. [x] **TSK-app-g2-3** Handler test: OLE magic + título + 16 cols (`mvn -pl application,infrastructure -am`) — Tests run 2+1 Failures 0 (2026-09-16)
6. [x] **TSK-web-g4-1** South Excel enabled; `lastFiltro`; download blob; 0 filas silencio
7. [x] **TSK-web-g5-1** e2e: botón enabled; download stub `.xls` tras Consultar — 5 passed (2026-09-16)
8. [x] **TSK-ops-g6-1** Smoke Francisco: abrir xls = grilla — 2026-09-16 «ok el excel esta saliendo bien»
9. [x] **TSK-ops-g6-2** Verify PASS + gobierno (ledger)

## Gate UI

| Path | Rol | DoD |
|------|-----|-----|
| `consulta.xhtml` L214–216 | South Excel 120px | **enable** el botón ya cableado T5.5; no layout nuevo |

**Prohibido:** emitter Excel del sidecar; SheetJS; fork ruta; habilitar equipo; Flyway; clonar `XLSParser` Faces.
