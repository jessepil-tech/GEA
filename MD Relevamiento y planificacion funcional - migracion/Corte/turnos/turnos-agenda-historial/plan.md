---
title: Plan — T6.3 · Historial Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial
---

# Plan — Historial Turnos

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Ninguno. Tabla dump / V43. |
| API | Query listar hist (JDBC `ts.hist_turno` + enrich labels/tel). Query detalle popup (reuso ficha/prep/req/docs + próximos). Resource delgado. |
| Web | Enable accordion. Vista north+tabla+leyenda; Excel disabled. Ícono info → dialog HIS. Reusar buscador personal T5. |
| Tests | Handler list vacío / con fila / hora inválida. e2e accordion + consultar + info. G6 con fila T5. |

Orden: Clarify **FIRME** → G0 inventarios → Gate UI **antes** de API-first → G2 Api → G4 Web → G5 e2e → G6.

Gate UI: **no** page nueva; es hoja del chrome Agenda.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + inventarios + reservas/fixture |
| G1 | N/A Flyway |
| G2 | Api GET hist + GET info + tests |
| G3 | N/A impresión (Excel = hijo) |
| G4 | Accordion live + north/tabla/popup; Excel disabled |
| G5 | Playwright e2e-migrado |
| G6 | Francisco: ve hist y abre Información Turno — «si se ve ok» 2026-09-18 |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Piloto `COUNT(hist_turno)=0` hoy | Fixture: ops T5 otorga/libera; no seed; si no hay acto → `diferido(fixture)` |
| Filtrar por call center «porque cola lo hace» | Prohibido — HIS no lo pasa al SELECT |
| Toast `_3` de fechas | Prohibido — el bean no lo tiene |
| Dialog T5.1e en lugar del hist | Layout HIS propio 1200×512 |
| `decode` Oracle en formulas | CASE PG en JDBC |
| Clonar BODY | JDBC equivalente |
