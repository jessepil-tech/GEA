---
title: Plan — T6.2 hijo · PDF cola Reasignación
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-print
---

# Plan — PDF cola Reasignación

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | **n/a** — tabla y package ya en dump/Reports |
| Reports | No re-portar layout. Package ya `NULLIF(..., 0)`. Default BIRT `idCentroAte=1` se pisa con `0` si Todos |
| API | `GetColaReasignarPdfQueryHandler` `REPORT_ID=TurnosAReasignar`. Resource GET `cola-reasignar/imprimir.pdf` |
| Web | Encender Imprimir south; blob como T5.5. Excel sigue disabled |
| Tests | Handler params; e2e stub blob; G6 visual motor real |

Orden: Clarify FIRME → G0 docs → G2 Api+Web → G5 e2e stub → G6 smoke PDF.

Gate UI **N/A** (no xhtml nuevo).

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + slug — 2026-09-17 |
| G1 | N/A Flyway |
| G2 | GET + Web Imprimir |
| G3 | Print-path en dump `_dev` 2026-09-17 (`p_get_turnos_a_reasignar` + personas tel/mail); Reports JDBC = misma base que Api |
| G4 | N/A UI nueva |
| G5 | Playwright stub enable + download |
| G6 | Francisco: PDF cola con info — **PASS** 2026-09-17 «listo ahi pude probar y sale info el reporte» |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Default BIRT `idCentroAte=1` | Mandar `0` si Todos (D-TUR-68) |
| Confundir con ConsultaAgenda | `reportId=TurnosAReasignar`; filename `turnos-a-reasignar-*` |
| Cola vacía (T4 otorgó) | G6 con `302675` o nueva fila T4; no seed |
| Reports stub | G6 exige sidecar HTTP; stub = FAIL visual |
