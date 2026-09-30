---
title: Verify — G1-d.birt
description: BIRT 4.24 ticket PDF
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-d-birt
---

# Verify — G1-d.birt

## verdict

**PASS** — BIRT 4.24 ReportEngine genera `NroColaEsperaRecep` (diseño adaptado PG)
vía `birt-worker`; Hospital-Reports IT PASS (`openpdf` en test); worker smoke PDF OK.

## CA

| CA | Resultado |
|----|-----------|
| PDF BIRT con texto ticket | pass (worker) |
| IT ReportsResourceIT | pass (%test openpdf) |
| Contrato Api/UI | sin cambio |
| WAIVE pixel / barcode / impresora | waived |

**WAIVE gate:** ~~pixel / barcode / logo~~ → **R3.1 ingeniería done** (2026-08-14).
Pendiente: **UAT impresora térmica on-site** (y ajuste pixel si UAT lo pide).

Barcode recepción: font DANI + `Interleaved25Encoder` (no OnBarcode). Triage:
`f_cod_barra_triage_amb` cuando exista CU.
## Cómo

```bash
cd Hospital-Reports
bash tools/fetch-birt-runtime.sh
bash tools/build-birt-worker.sh
set -a && source ../Hospital-Legacy/tools/postgres-host.env && set +a
mvn quarkus:dev   # :8082 hospital.reports.engine=birt
```
