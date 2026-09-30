---
title: Verify — T6 · migrar turno vencido
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-ciclo-vida.verify
---

# Verify — Migrar turno vencido

**Gate:** **gate-done** 2026-09-21. Clarify **FIRME** D-TUR-75 (Francisco).  
**No** cierra T7 avisos, `CheckHabTurnosJob`, ni el satélite `tarea_programada`.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Archivar oferta < hoy | **done** | Camino 1 · `id_turno_vencido=19999001` |
| Purga cola 6 h | **done** | COUNT candidatos = 0 · POST `colasMarcadas=0` (vacío) |
| Copiar mensajes vigentes | **done** | path en JDBC; fixture G6 sin `mensaje_turno` (0 copiados) |
| Expire TV 1 h | **N/A** | ya cobrado |
| Alta REPROGRAMACION | **diferido** | T7 avisos |
| MailTurnoJob | **diferido** | T7 avisos |
| CheckHabTurnosJob | **N/A** | T2 |
| Disconnect Oracle | **N/A** | no se porta |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Acto de usuario en Web? | no |
| Decisión | **N/A** (proceso programado; sin hoja) |
| Viaje (pasos) | — |
| Fixture | slot `fecha_hora_tur_ini` < trunc(hoy) · `id_turno=19999001` LIBRE ayer (SQL, no T4) |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Build/test command + comando | build/test | `mvn -pl core,presentation-api -am test -Dtest=MigrarTurnoVencidoEngineGoldenMasterTest,TurnosVencidosResourceIT` · core **Tests run: 2 Failures: 0**. IT QuarkusTest no arrancó (Docker Dev Services ausente). | Hospital-Api 2026-09-21 | **verificado** |
| 2 | POST migrar status | endpoint | `POST /api/v1/turnos/vencidos/migrar` Bearer admin → **200** `turnosArchivados=1` `colasMarcadas=0` `idsArchivados=[19999001]` · 1457 ms | dump `grupogea-hospital_dev` 2026-09-21 | **verificado** |
| 3 | Escritura `turno_vencido` id nacido del CU | escritura | `id_turno_vencido=19999001` · `SELECT id_turno_vencido, estado_turno, fecha_hora_tur_ini FROM ts.turno_vencido WHERE id_turno_vencido=19999001` → `19999001 \| LIBRE \| 2026-09-20 10:00:00` · `SELECT id_turno FROM ts.turno WHERE id_turno=19999001` → 0 filas · CU POST migrar | dump 2026-09-21 | **verificado** |
| 4 | Golden `f_migra_turno_vencido` (recorte C1) | test | `MigrarTurnoVencidoEngineGoldenMasterTest` · 7 casos JSON (null / vacío / ayer / hoy-medianoche / hoy-mañana / LIBRE ayer / OTORGADO ayer) + `dosActores_mismoCriterio_ambosArchivan` · Tests run: 2 | Hospital-Api 2026-09-21 | **verificado** |
| 5 | Rollback a mitad (turno + mensaje) | test | POST **500** `fk_13_turno` (hoy `19999006.id_turno_inhibe` → ayer `19999005`). Tras el 500: ambos siguen en `ts.turno`; `SELECT id_turno_vencido FROM ts.turno_vencido WHERE id_turno_vencido=19999005` → 0 filas (INSERT revertido). Fixtures borrados. | dump 2026-09-21 | **verificado** |
| 6 | Acceso denegado: POST sin JWT → 401 | endpoint | `POST http://localhost:8081/api/v1/turnos/vencidos/migrar` sin Bearer → **401** | 2026-09-21 | **verificado** |
| 7 | p95 / COUNT volumen / dos actores `FOR UPDATE` | no funcional | COUNT dump `fecha_hora_tur_ini < trunc(hoy)` = **0** (medido en vacío) → **diferido(perf-volumen)**. Cola 6 h candidatos = 0. Oferta hoy = 116. Dos POST paralelos sobre `id_turno=19999003`: A **200** `turnosArchivados=0` vs B **200** `idsArchivados=[19999003]`. p95 n=1 escritura **1457 ms** (presupuesto ≤ 1,5 s). | dump 2026-09-21 | **verificado** |
| 8 | G6 operador | e2e | Francisco 2026-09-21 «ok». `19999001` archivado; sentinel hoy `id_turno=17255155` sigue `SUSPENDIDO` en `ts.turno`. | operador | **verificado** |
