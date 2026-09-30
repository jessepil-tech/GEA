---
title: Plan — T5.5 hijo · Excel consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export
---

# Plan — Excel Consulta Agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Ninguno. |
| API | `ExcelExportPort` + POI HSSF en infrastructure. Handler `GetConsultaAgendaExcel*` reusa `TurnosAgendaPort.listarConsultaRango`. Resource GET `…/consulta/exportar.xls`. Vacío → 204. JDBC consulta: `nro_hc_anterior`, tel `te_persona`, mail NULL. |
| Web | Quitar `disabled`. `lastFiltro` al Consultar. Download blob como Imprimir. |
| Tests | Handler unitario (magic OLE + columnas). e2e stub `.xls`. G6 archivo real. |

Orden: Clarify FIRME → G0 docs → G2 Api → G4 botón → G5 e2e → G6 smoke.

Gate UI: **chrome existente**; no template nuevo. Inventarios G0 **antes** de habilitar el click.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + inventarios + reservas/fixture |
| G1 | N/A Flyway |
| G2 | Api GET xls + tests handler |
| G3 | N/A Reports |
| G4 | South Excel enabled + lastFiltro + download |
| G5 | Playwright e2e-migrado (stub CI) |
| G6 | Francisco: Excel abre con filas = grilla — **PASS** 2026-09-16 «ok el excel esta saliendo bien» |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| POI en presentation-api | Port en core; adapter infrastructure; ArchUnit |
| North sucio ≠ listTurnos HIS | `lastFiltro` al Consultar |
| `mail_persona` ausente | columna presente, valor NULL |
| Clonar XLSParser (Faces) | Writer acotado a este acto |
