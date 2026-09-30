---
title: Plan — T6.2 hijo · Excel cola Reasignación
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-export
---

# Plan — Excel cola Reasignación

| Capa | Decisión |
|------|----------|
| DDL | n/a |
| Api | `GetColaReasignarExcelQueryHandler` + `ExcelExportPort` (ya T5.5-excel) |
| Web | Encender Exportar Excel; blob `.xls` |
| Tests | Handler 17 cols; e2e stub; G6 abrir archivo |

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME D-TUR-69 |
| G2 | GET + botón |
| G5 | Playwright stub |
| G6 | Francisco abre el xls — **PASS** 2026-09-17 |

Gate UI **N/A**.
