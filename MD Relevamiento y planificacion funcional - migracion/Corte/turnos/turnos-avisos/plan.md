---
title: Plan — T7 · avisos de turno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-avisos
---

# Plan — Avisos de turno

## Enfoque

Cómo se ejecuta **Camino 1** (FIRME D-TUR-76):

| Capa | Decisión |
|------|----------|
| DDL | Ninguno. Tablas en dump. |
| API | (1) Enganche en el mismo CU de T6 (`MigrarTurnoVencido`): *antes* del DELETE, si `OTORGADO` y flags de hab/centro, nace `mensaje_turno_vencido` tipo `REPROGRAMACION` (mail y/o SMS) con texto de `GENERA_MAILS`. Housekeeping 7 d / server null al final del mismo comando. (2) Command `DespacharMensajeTurno` (CQRS). Resource delgado `POST /api/v1/turnos/avisos/despachar` para G6. Scheduler Quarkus `@Scheduled` (SKIP), intervalo HIS `MINUTOS/2`, flag propio `features.enable-mail-turno` — **no** acoplar a `enable-background-jobs` ni al flag de T6. Server mail/SMS null → warn HIS, **no** gateway. |
| Web | **N/A** — no hay hoja. |
| Tests | Golden plantilla: flags N/null → no nace; flags S + cuerpo → texto no vacío; paciente sin mail; `id_turno` ya vencido. IT: POST despachar sin JWT → 401; dos actores mismo `id_mensaje`; housekeeping marca `enviado='S'`. G6 SQL (id de fila en `mensaje_*` **o** COUNT 0 = paridad Demo). |

Orden: Clarify **FIRME** → G2 Api (enganche T6 + job) → G5 N/A → G6. Sin Gate UI.

No clonar `Hospital-Legacy/SCHEDULER`. Cáscara HIS: `MailTurnoJob` → `Interfaces` envío por centro. Acá: scheduler/POST → handler → port JDBC. El texto lo arma el motor portado, no el WAR.

No backfill del OTORGADO `17255210` ya archivado el 2026-09-22 (el HIS genera *antes* del DELETE; esa fila ya no está en `ts.turno`).

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | FIRME + inventarios + COUNT `mensaje_*` + flags centro/hab |
| G1 | N/A Flyway |
| G2 | Plantilla golden + enganche archivo + housekeeping + POST despachar + scheduler + IT |
| G3 | N/A (ticket PDF es hijo) |
| G4 | N/A Web |
| G5 | Playwright **N/A** |
| G6 | Francisco: o bien nace un `id_mensaje` `REPROGRAMACION` (si se prenden hab+cuerpo), o bien 0 filas con Demo actual (paridad HIS) |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Demo sin server ni cuerpo reasigna | G6 = 0 filas es paridad; fila >0 exige config padres (no seed de `mensaje_*`) |
| Plantilla lee `ts.turno` y T6 ya borró | mismo TX, *antes* del DELETE; no backfill |
| `enable-background-jobs` / flag T6 | flag **dedicado** `enable-mail-turno`; intervalo 2 min configurable |
| Volumen cola 0 | «medido en vacío» + `diferido(perf-volumen)` |
| SMTP de verdad | `diferido(smtp)`; no inventar gateway |
| Tablero BODY | un owner `turnos-avisos`; D-TUR-17 no arranca hasta liberar |
