---
title: Tasks — Piloto AGI G1-d
description: Checklist implementación G1-d
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-d
---

# Tasks — G1-d

- [x] **TSK-docs-1** — SDD + backlog/estado/dossier/birt-runtime (R3)
- [x] **TSK-rpt-1** — Scaffold `Hospital-Reports` (:8082) health + run
- [x] **TSK-rpt-2** — Engine OpenPDF `NroColaEsperaRecep`
- [x] **TSK-api-1** — `ReportsPort` + HTTP/stub adapters + config
- [x] **TSK-api-2** — `findRecepcion` + `GET …/ticket.pdf` + `ticketPdfUrl`
- [x] **TSK-web-1** — Enlace descarga PDF en pantalla ticket
- [x] **TSK-ops-1** — Smoke `smoke-piloto-agi-g1d.sh` + IT
- [x] **TSK-ops-2** — verify-report PASS / gate-done
- [x] **TSK-birt-1** — Swap engine → BIRT 4.24 + diseño PG → ver [`piloto-agi-g1-d-birt/`](../piloto-agi-g1-d-birt/)
