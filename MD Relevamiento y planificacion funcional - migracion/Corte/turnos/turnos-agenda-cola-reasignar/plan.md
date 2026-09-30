---
title: Plan — T6.2 · cola Reasignación de Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-cola-reasignar
---

# Plan — Cola Reasignación de Turnos

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Ninguno. Tabla V43. |
| API | Query listar cola (JDBC `ts.turno_a_reasignar` + enrich labels/tel). Command UPDATE observaciones. Resource delgado. IrAGrilla = estado Web, no write. |
| Web | Enable accordion. Vista north+tabla+south (Imprimir/Excel disabled). Gear → Agenda T6.1. Popup obs. Reusar buscador personal T5. |
| Tests | Handler list vacío / con fila / intervalo inválido. UPDATE obs id vigente. e2e accordion + consultar + ir a grilla. G6 con fila T4. |

Orden: Clarify **FIRME** → G0 inventarios (ya borrador) → Gate UI chrome accordion **antes** de API-first → G2 Api → G4 Web → G5 e2e → G6.

Gate UI: **no** template de página nueva; es hoja del chrome Agenda. Inventarios **antes** de habilitar el click.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + inventarios + reservas/fixture (COUNT cola) |
| G1 | N/A Flyway |
| G2 | Api GET cola + PATCH obs + tests |
| G3 | N/A impresión (hijo) |
| G4 | Accordion live + north/tabla + gear + popup obs; south disabled |
| G5 | Playwright e2e-migrado |
| G6 | Francisco: ve la cola y REASIGNAR TURNO abre Agenda |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Piloto `COUNT(turno_a_reasignar)=0` | Fixture: ops T4 eliminar; no seed; si no hay acto → `diferido(fixture)` |
| `id_turno` HIS = PK cola | Documentar en DTO; no joinear `ts.turno.id` por error |
| Overlay T6.1 desde cola | Paridad: **no** pintar `PENDIENTE_LIBERAR` (`turnoAReasignar=true`) |
| Clonar BODY / GTT | JDBC equivalente; print-path queda en el hijo |
| Auditoría TBL_AUD | Chequear al implementar; si falta → `diferido(auditoria)` |
