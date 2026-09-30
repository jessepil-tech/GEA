---
title: Tasks — Piloto AGI G1
description: Checklist ordenada slice recepción autogestión.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.piloto-agi-g1
---

# Tasks — Piloto AGI G1

## `sdd.hospital.piloto-agi-g1.phase-0` — Spec/plan

1. [x] **TSK-ops-1** Spec + plan + tasks + README en `docs/sdd/piloto-agi-g1/`.
2. [x] **TSK-ops-2** Clarify Q1–Q3 aceptados (defaults) → spec/plan **reviewed** (2026-08-13).
3. [x] **TSK-ops-3** verify-report pre-implement **PASS** (2026-08-13).

## `sdd.hospital.piloto-agi-g1.phase-1` — API

4. [x] **TSK-app-1** Flyway seed: terminal, paciente, turnos demo (`V8__piloto_agi_g1.sql`).
5. [x] **TSK-app-2** `POST .../identificar` + `GET .../turnos`.
6. [x] **TSK-app-3** `POST .../recepciones` → ticket.
7. [x] **TSK-app-4** `GET .../terminales`.

## `sdd.hospital.piloto-agi-g1.phase-2` — Front + smoke

8. [x] **TSK-app-5** Wizard Hospital-Web `/agi/recepcion`.
9. [x] **TSK-app-6** Smoke `tools/smoke-piloto-agi-g1.sh`.
10. [x] **TSK-ops-4** verify-report post-implement + dossier.
    - **Hecho 2026-08-13:** [verify-report.md](verify-report.md) **PASS** + `gate-done`
      (smoke PASS; identificar 404; Web :4210 up).

## Orden

```text
ops-1 → ops-2 → ops-3 → app-1 → app-2 → app-3 → app-4 → app-5 → app-6 → ops-4
```

## Mapa RF → TSK

| RF / INV | Tarea |
|----------|--------|
| RF-1 | TSK-app-1, app-4 |
| RF-2 RF-6 | TSK-app-2 |
| RF-3 | TSK-app-2 |
| RF-4 | TSK-app-3 |
| RF-5 | TSK-app-5, app-6 |
| NFR-1…3 INV-* | plan + app-1…3 |
| CA verify | TSK-ops-3, ops-4 |
