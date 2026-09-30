---
title: Plan — Piloto AGI G1-d
description: Sidecar reports + cliente Api + UI PDF.
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-d
---

# Plan — Piloto AGI G1-d

Estado: **reviewed** (alcance acordado 2026-08-14).

## Enfoque

1. Repo/servicio hermano **`Hospital-Reports`** (Quarkus JVM, :8082): health +
   `POST /api/v1/reports/run`.
2. Engine v1: `NroColaEsperaRecepParamEngine` (OpenPDF). Interfaz
   `ReportEngine` lista para `BirtReportEngine` (G1-d.birt).
3. **Hospital-Api**: `ReportsPort` + HTTP adapter; `findRecepcion`; query/command
   para PDF; campo `ticketPdfUrl` en ticket.
4. **Hospital-Web**: descarga autenticada del PDF.
5. Smoke `smoke-piloto-agi-g1d.sh`.

## Decisiones

| Tema | Decisión |
|------|----------|
| Aislamiento | Proceso aparte; cero `org.eclipse.birt` en Api |
| Puerto reports | **8082** |
| Auth sidecar | Red local / sin JWT en v1 (solo Api expone al cliente) |
| Motor gate | OpenPDF param; BIRT 4.24 = follow-up |
| Impresora | WAIVE |
| Datasource sidecar | No JDBC obligatorio en gate (params only) |
| Fallo sidecar | Domain/Application → 502 |

## API

| Método | Ruta | Notas |
|--------|------|-------|
| POST | `/api/v1/reports/run` (Reports) | body `{ reportId, format, parameters }` → PDF |
| GET | `/api/v1/agi/recepciones/{id}/ticket.pdf` (Api) | JWT; proxy al sidecar |
| POST | `/api/v1/agi/recepciones` | + `ticketPdfUrl` |

## Verificación

- IT: PDF magic bytes con `hospital.reports.mode=stub`
- Smoke host: Reports:8082 + Api:8081 + Identity
- UI: enlace visible post-confirm
