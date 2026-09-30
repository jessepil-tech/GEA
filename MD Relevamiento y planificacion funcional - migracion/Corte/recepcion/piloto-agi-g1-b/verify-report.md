---
title: Verify report — sdd.hospital.piloto-agi-g1-b
description: Envelope verify G1-b.
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-b
---

# Verify report — `sdd.hospital.piloto-agi-g1-b`

## metadata

| Campo | Valor |
|-------|--------|
| date | 2026-08-14 |
| fase | post-implement (TSK-ops-4) |

## verdict

**PASS** — slice G1-b **gate-done**.

IT `AgiRecepcionResourceIT` 5/5 + smoke host Identity:8080 → Api:8081 **PASS**
(`smoke-piloto-agi-g1b.sh`).

## checklist

| # | Criterio | Resultado | Notas |
|---|----------|-----------|-------|
| V1 | CA comprobables | pass | CA 1–5 |
| V2 | Plan alineado | pass | seed, sin WS/hardware |
| V3 | Tasks | pass | app-1…6 + ops-4 |
| V4 | Verificación runtime | pass | smoke PASS |
| V5 | Sin secretos | pass | |

## Criterios de aceptación

| CA | Evidencia | Resultado |
|----|-----------|-----------|
| 1 Autorizado | smoke DNI 30111222 → ESPERA_ATENCION | pass |
| 2 Rechazado | smoke DNI 30999888 → ESPERA_RECEPCION | pass |
| 3 API Key | smoke GET terminales X-API-Key | pass |
| 4 UI destino | Web `/agi/recepcion` | pass |
| 5 SDD | este reporte | pass |

## commands

```bash
set -a && source tools/postgres-host.env && set +a
# Identity :8080 + Hospital-Api :8081 (quarkus:dev)
bash Hospital-Api/tools/smoke-piloto-agi-g1b.sh   # PASS 2026-08-14
```

## blockers

Ninguno.

## warnings

| id | description |
|----|-------------|
| W1 | Adapter seed ≠ ValidadorWS real |
| W2 | Credencial/token UI = G1-c |
| W3 | GM Oracle elegibilidad diferido |

## architecture_invariants

| invariant | status |
|-----------|--------|
| INV-1 Identity sin elegibilidad clínica | ok |
| INV-2 No microservicio Core | ok |
