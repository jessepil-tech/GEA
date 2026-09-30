---
title: Plan — T5 hijo · PDF turno (Agenda Imprimir)
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-agenda-imprimir-turno
---

# Plan — PDF turno Agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | **n/a** |
| Reports | No re-portar layout. Example `Turno.json` |
| API | `GetImprimirTurnoPdfQueryHandler` `REPORT_ID=Turno`. Resource GET `{idTurno}/imprimir.pdf`. Lee turno+nacimiento; edad HIS |
| Web | Ítem Imprimir en gear; blob como cola reasignar |
| Tests | Handler params; e2e stub blob; G6 visual motor real |

Orden: Clarify FIRME → G0 docs → G2 Api+Web → G5 e2e stub → G6 smoke PDF.

Gate UI **N/A** (no xhtml nuevo). Copy `Imprimir` ya en `turnos-agenda-labels.ts`.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + slug — 2026-09-22 |
| G1 | N/A Flyway |
| G2 | GET + Web Imprimir |
| G3 | Sidecar `Turno` ya en Reports |
| G4 | N/A UI nueva (solo menú) |
| G5 | Playwright stub enable + download |
| G6 | Francisco: gear → Imprimir → PDF con info — **PASS** 2026-09-22 «joya sale bien el pdf» |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Confundir con ConsultaAgenda / ticket AGI | `reportId=Turno`; filename `turno-{id}.pdf` |
| `f_imprime` como si fuera el PDF | El SP no alimenta el layout; solo coseguro → diferido |
| Default `urlLogo` GEA | Mandar `SDLC_VM` (JSON canónico) |
| Reports stub | G6 exige sidecar HTTP; stub = FAIL visual |
