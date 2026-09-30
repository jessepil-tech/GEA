---
title: README — Piloto AGI G1-b
description: Índice SDD G1-b.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-b
---

# Piloto AGI G1-b — elegibilidad

Extiende G1 con el **gate de elegibilidad** (puerto + seed) y destino de ticket
(`ESPERA_ATENCION` vs `ESPERA_RECEPCION`). Sin ValidadorWS real ni hardware.

| Artefacto | Archivo |
|-----------|---------|
| Spec | [spec.md](spec.md) |
| Plan | [plan.md](plan.md) |
| Tasks | [tasks.md](tasks.md) |
| Verify | [verify-report.md](verify-report.md) |

Demo: DNI `30111222` (autorizado) · DNI `30999888` (rechazado → recepción humana).
