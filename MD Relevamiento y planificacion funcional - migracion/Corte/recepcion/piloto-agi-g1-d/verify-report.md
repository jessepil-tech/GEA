---
title: Verify report — sdd.hospital.piloto-agi-g1-d
description: Post-implement G1-d (camino PDF)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-d
---

# Verify — G1-d

## verdict

**PASS** / **gate-done** — IT + smoke host (Identity:8080 → Api:8081 → PDF).

Motor BIRT 4.24: **WAIVED** → `TSK-birt-1` (contrato HTTP estable; OpenPDF param en gate).

## CA

| CA | Resultado |
|----|-----------|
| ticketPdfUrl en 201 | pass |
| GET ticket.pdf → %PDF | pass |
| Sidecar / stub | pass (dev http + Reports :8082; IT stub) |
| Smoke / IT | pass (`smoke-piloto-agi-g1d.sh` + `AgiRecepcionResourceIT`) |
| UI descarga | pass (código) |
| WAIVE impresora / BIRT 4.24 visual | waived |

## Evidencia

- `Hospital-Reports` :8082 — OpenPDF `NroColaEsperaRecep`
- Api: `ReportsPort` + `GET /api/v1/agi/recepciones/{id}/ticket.pdf`
- Smoke: `Hospital-Api/tools/smoke-piloto-agi-g1d.sh` PASS 2026-08-14
