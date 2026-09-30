---
title: SDD — Piloto AGI G1-d.birt
description: Swap OpenPDF → Eclipse BIRT 4.24 (NroColaEsperaRecep).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-d-birt
---

# Spec — G1-d.birt

Padre: [G1-d](../piloto-agi-g1-d/). Validar **ReportEngine BIRT 4.24** en
`Hospital-Reports` con el diseño `NroColaEsperaRecep` (adaptado PG).

## Resultado

1. Runtime Eclipse `birt-runtime-4.24` en `.birt-runtime/ReportEngine` (script
   `tools/fetch-birt-runtime.sh`).
2. Worker JVM `birt-worker.jar` (aislado del ClassLoader Quarkus).
3. `hospital.reports.engine=birt` → PDF desde `.rptdesign`; fallback OpenPDF si
   faltan artefactos.
4. Diseño adaptado: JDBC PG, SQL sin Oracle/`dual`, barcode vacío (sin triage),
   logo → label (sin archivo).
5. Misma API/UI G1-d (`ticket.pdf`).

## CA

1. Worker o `POST /reports/run` con engine=birt → `%PDF` + texto de ticket
   (paciente / nro espera).
2. IT sigue PASS con `%test.hospital.reports.engine=openpdf`.
3. Api/Web sin cambios de contrato.
4. WAIVE **del gate G1-d.birt** (no del corte): pixel-perfect vs legacy;
   barcode triage; logo real; impresora.

## Deuda abierta → plan (obligatoria)

Estos ítems **hay que hacerlos** antes de UAT/corte del ticket; no son
opcionales. Track canónico:

[`birt-runtime-destino.md`](../../../arquitectura/birt-runtime-destino.md) **§4.1 / R3.1** ·
backlog [`backlog-orden-2026-08-14.md`](../../../planificacion/backlog-orden-2026-08-14.md).

| Deuda | Estado |
|-------|--------|
| Logo real (`urlLogoSmall` / asset) | TODO |
| Barcode (plugin + dataset) | TODO (R2 + R4/R5) |
| Paridad layout vs legacy | TODO (R7, muestra ticket AGI) |

## Fuera

- Otros `.rptdesign`; embeber BIRT en Hospital-Api.
