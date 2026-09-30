---
title: Tasks — CU Llamar atención médica
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Tasks — Llamar atención médica

## E0 — Clarify / docs

1. [x] **TSK-ops-e0-1** Firma producto Clarify (#1–#4) en [spec.md](spec.md) (2026-09-16)
2. [x] **TSK-ops-e0-2** Abrir SDD + link matriz / mapa / ciclo-vida (2026-08-27)

## E1 — Puerto escritura clínico

Owner: [`cola-b-llamar/`](../cola-b-llamar/) C1.

3. [x] **TSK-app-e1-1** Extender `AnunciadorWritePort` (paciente, servicio, cola serv amb, tipo_atencion)
4. [x] **TSK-app-e1-2** Adapter JDBC: INSERT fiel + re-call DELETE + WS; path Cola A intacto
5. [x] **TSK-app-e1-3** IT insert clínico (`tipo_atencion=ATENCION_MEDICA`, FKs)

## E2 — API

Owner: [`cola-b-llamar/`](../cola-b-llamar/) C2. Ambiente↔anunciador = `anunciadorId` demo.

6. [x] **TSK-app-e2-1** `POST` llamar desde `id_cola_espera_serv_amb`
7. [ ] **TSK-app-e2-2** Gate sin anunciador vinculado → 4xx/paridad (`diferido` cola-b-llamar)
8. [x] **TSK-app-e2-3** IT resource (`RecepcionEsperaAmbResourceIT`)

## E3 — UI

9. [x] **TSK-app-e3-1** Ruta `/ambulatoria/espera-atencion` + gear Llamar (no Cola B recepción)
10. [x] **TSK-app-e3-2** Menú ATENCION MEDICA + mapa

## E4 — Verify

11. [x] **TSK-ops-e4-1** Smoke UI en [verify-report.md](verify-report.md) (e2e live 2026-09-16)
12. [x] **TSK-ops-e4-2** Capacidades legacy: done / diferido(slug) — sin silencios
13. [ ] **TSK-f4** No borrar evidencia (`turno` 1–6, cola B 5005–5010, llamado id=5). El id=6 lo reemplazó NFR 6b (ahora 26).
