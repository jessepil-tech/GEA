---
title: Tasks — E2E mostrador
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.e2e-mostrador
---

# Tasks — E2E mostrador

- [x] **TSK-f0** Ids vigentes en `hospital_api` (README Fixture 2026-09-16). T2/T3/LIBRE/terminal se arman vía CU en el spec si faltan.
- [x] **TSK-f1** Spec Playwright `e2e/e2e-mostrador.spec.ts` contra Api real (no `mockTurnosAgendaApi`). Corre con `E2E_MOSTRADOR_LIVE=1`.
- [x] **TSK-f2** Viaje ejecutado: `ts.turno` id=5 `RECEPCIONADO` + cola B id=5009 + `llamado_anunciador` id=5 (TV ANU-DEMO). Conservar id=4 / cola 5008.
- [x] **TSK-f3** Verify: T5→AGI→Cola B→Llamar→TV `e2e-migrado` (writer `cola-b-llamar` Clarify B).
- [ ] **TSK-f4** No borrar las filas de evidencia (`ts.turno` 1–7, cola B 5005–5010, llamado id=5). id=7 = NFR dos actores (quedó `OTORGADO`).
- [x] **TSK-f5** Acceso 401 sin Bearer en T5 / AGI confirmar / Llamar Cola B (live 2026-09-17). JWT sin rol de menú = `diferido(acceso)`.
- [x] **TSK-f6** NFR p95 de **alta** Llamar + dos actores sobre un `id_turno` disputable. 2026-09-17: 20× POST 5010 → **200** p95 **6,0 ms**; 2× `/reservar` id=7 → 200 vs 400 ocupado; 2× `/otorgar` → 200 vs 404. Volumen fixture = `diferido(perf-volumen)`.
