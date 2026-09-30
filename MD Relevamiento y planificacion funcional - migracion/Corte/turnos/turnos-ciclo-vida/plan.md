---
title: Plan — T6 · migrar turno vencido
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-ciclo-vida
---

# Plan — Migrar turno vencido

## Enfoque

Cómo se ejecuta **Camino 1** (si FIRME):

| Capa | Decisión |
|------|----------|
| DDL | Ninguno. Tablas en dump. |
| API | Command `MigrarTurnoVencido` (CQRS). JDBC con `FOR UPDATE` (paridad cursor). Resource delgado `POST /api/v1/turnos/vencidos/migrar` para G6. Scheduler Quarkus `@Scheduled` (SKIP concurrente), intervalo configurable (~41 min HIS), flag propio — **no** acoplar a `features.enable-background-jobs` (enciende el `DataCleanupJob` no-op del starter). |
| Web | **N/A** — no hay hoja. |
| Tests | Golden: vacío, LIBRE ayer, OTORGADO ayer, hoy no archiva, rollback. IT dos actores + `FOR UPDATE`. G6 SQL (ids en `turno_vencido`, ausentes en `turno`). |

Orden: Clarify **FIRME** → G2 Api → G5 N/A Playwright → G6. Sin Gate UI.

No clonar `Hospital-Legacy/SCHEDULER`. La cáscara HIS era `MigrarTurnoVencidoJob` → `Interfaces.migrarTurnoVencido` → `{? = call TS.TURNOS.f_migra_turno_vencido()}`. Acá: scheduler/POST → handler → port JDBC.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | FIRME + inventarios + COUNT oferta `fecha < trunc(hoy)` |
| G1 | N/A Flyway |
| G2 | Command + JDBC + scheduler + POST + golden + IT rollback |
| G3 | N/A reportes |
| G4 | N/A Web |
| G5 | Playwright **N/A** |
| G6 | Francisco: un LIBRE ayer ya no está en oferta viva y sí en `turno_vencido` |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Job muta: no capturar oráculo 11.2 | Golden por casos del BODY, no llamar la función en prod Oracle |
| mindate T4 = hoy | fixture pasado declarado ahora; si el motor no acepta → `diferido(fixture)` |
| Duplicar expire 1 h | reusar el port existente; no segundo `UPDATE` |
| `enable-background-jobs` | flag **dedicado**; no despertar `DataCleanupJob` |
| Volumen prod vs dump 126 | `diferido(perf-volumen)` si se mide en vacío |
| Tablero BODY | un owner; e2e-mostrador **no** porta TURNOS |
