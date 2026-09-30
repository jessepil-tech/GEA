---
title: Inventario interacción — T5.5 hijo Excel
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export.interaccion
---

# Inventario interacción — Excel Consulta Agenda

Fuente: `consulta.xhtml` L214–216 · `BBConsultaAgenda.generarReporteExcel`.

| Control | Disparador | Efecto HIS | Web |
|---------|------------|------------|-----|
| Exportar Excel | `commandButton` `actionListener="#{bbConsultaAgenda.generarReporteExcel()}"` process `@this, formNorth` | Si `listTurnos` vacío: **noop**. Si hay filas: `XLSParser` → download `.xls` | **live** GET blob; `lastFiltro`; 0 filas no llama |
| Imprimir | T5.5 | PDF sidecar | **fuera** (no este corte) |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Auto-abrir Excel al Consultar | Prohibido — hace falta clic |
| Toast «no hay datos» | Prohibido — HIS silencio |
| SheetJS / CSV | Prohibido — POI `.xls` |
| Fork `/turnos/consulta-excel` | Prohibido |
