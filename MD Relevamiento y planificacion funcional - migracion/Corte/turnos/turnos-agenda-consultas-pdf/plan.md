---
title: Plan — T5.5 hijo · PDF consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-pdf
---

# Plan — PDF Consulta Agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | G1: `V56` `ts.turno_vencido` IF NOT EXISTS (columnas de `ts.turno` + PK `id_turno_vencido`). `ts.equipo_serv_centro` IF NOT EXISTS vacía (nombres Oracle; tipos catálogo). Sin seed de histórico ni de catálogo equipo. |
| Reports | `NULLIF(..., 0)` en ids de `p_get_turnos_fecha`. Quitar default `44983` de `ConsultaAgenda.rptdesign`. Reinstall package en piloto. **No** re-portar layout. |
| API | `GetConsultaAgendaPdfQueryHandler`: ids null/≤0 → no mandar `""` (omitir key o no filtrar). Tests handler. Resource ya T5.5. |
| Web | Sin cambio de pantalla. Imprimir ya live. |
| Tests | IT/handler params; e2e **N/A** (stub CI ya T5.5). |

Orden: Clarify FIRME → G0 docs → G1 Flyway → G2 package+Api params → G3 SQL dump → G6 smoke PDF visual.

Gate UI **N/A** (no xhtml nuevo).

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + inventario DDL — 2026-09-15 |
| G1 | `V56` `turno_vencido` + `equipo_serv_centro` IF NOT EXISTS (un writer) |
| G2 | Package `NULLIF` 0 + Api ids Todos + quitar default 44983 |
| G3 | En dump: `p_get_turnos_fecha` **sin** ERROR; count ≈ `ts.turno` hoy |
| G4 | N/A UI |
| G5 | Playwright **N/A** (motivo: no pantalla nueva; stub T5.5) |
| G6 | Francisco: PDF hoy con valores — **PASS** 2026-09-15 «ok ahora se está visualizando información en los pdf» |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Shape `turno_vencido` incompleto vs package | Copiar columnas que el SELECT lista desde `ts.turno` + `id_turno_vencido`; IF NOT EXISTS por si Reports-full ya la tiene |
| `equipo_serv_centro` abre D-TUR-17 | Tabla **vacía**; combo UI sigue disabled |
| Default 44983 sigue filtrando tras DDL | G2 params **después** de G1; probar Todos y con centro |
| Job T6 se cuela | Spec: 0 filas; histórico diferido |
