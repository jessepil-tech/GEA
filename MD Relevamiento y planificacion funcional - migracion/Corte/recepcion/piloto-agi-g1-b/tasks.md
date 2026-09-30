---
title: Tasks — Piloto AGI G1-b
description: Checklist G1-b elegibilidad.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-b
---

# Tasks — Piloto AGI G1-b

## phase-0

1. [x] **TSK-ops-1** Spec + plan + tasks + README.
2. [x] **TSK-ops-2** Clarify Q1–Q3 (defaults).
3. [x] **TSK-ops-3** verify-report pre-implement PASS.

## phase-1 API

4. [x] **TSK-app-1** Flyway V12 (convenio, seed elegibilidad, columnas ticket, paciente rechazado).
5. [x] **TSK-app-2** `ValidadorElegibilidadPort` + `SeedValidadorElegibilidadAdapter`.
6. [x] **TSK-app-3** `confirmarRecepcion` ramifica destino; ampliar `RecepcionTicket`.
7. [x] **TSK-app-4** IT autorizado + rechazado + API Key — `AgiRecepcionResourceIT` 5/5 PASS.

## phase-2 Front

8. [x] **TSK-app-5** Hospital-Web destino/mensaje + hint DNI rechazado.
9. [x] **TSK-app-6** `tools/smoke-piloto-agi-g1b.sh`.
10. [x] **TSK-ops-4** verify-report post-implement + smoke host PASS → **gate-done**.
