---
title: Verify — T7 · avisos de turno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-avisos.verify
---

# Verify — Avisos de turno

**Gate:** **gate-done** 2026-09-22. Clarify **FIRME** D-TUR-76 (Francisco).  
**No** cierra ticket PDF turno, confirmación al otorgar, SMTP real, ni `EnvioMailsJob`.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Nace `REPROGRAMACION` al archivar OTORGADO | **done** | `id_mensaje_turno_vencido=36222854` |
| Despacho `MailTurnoJob` (sin gateway si server null) | **done** | POST 200 `pendientesVistos=0`; fila `enviado=S` por housekeeping |
| Housekeeping 7 d / server null | **done** | misma fila `enviado=S` (a_enviar 2026-09-22 10:00) |
| SMTP/SMS real | **diferido(smtp)** | — |
| Confirmación / cancelación / recordatorio | **diferido** | hijo |
| `ProcesarSmsJob` / WhatsApp / mailbox genérico | **diferido** / **N/A** | — |
| Ticket PDF turno | **diferido** (hijo **gate-done**) | [`turnos-agenda-imprimir-turno/`](../turnos-agenda-imprimir-turno/) |
| `CheckHabTurnosJob` | **N/A** | T2 |
| Archivo oferta < hoy | **N/A** | T6 (ya cobrado) |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Acto de usuario en Web? | no |
| Decisión | **N/A** (proceso programado; sin hoja) |
| Viaje (pasos) | — |
| Fixture | Demo 1001; paso 2: hab+cuerpo + mail padre `20001` (revertidos); OTORGADO `19999101` ayer |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Build/test command + comando | build/test | `mvn -pl core test -Dtest=GeneraMailsReprogramacionEngineGoldenMasterTest` · exit 0 (2 tests, re-run post-REPLACE). compile presentation-api exit 0 | Hospital-Api `dev/t7-turnos-avisos` 2026-09-22 | **verificado** |
| 2 | POST despachar status | endpoint | `POST /api/v1/turnos/avisos/despachar` Bearer admin → **200** `pendientesVistos=0` | dump 2026-09-22 | **verificado** |
| 3 | Fila `mensaje_turno_vencido` nacida del CU | escritura | `id_mensaje_turno_vencido=36222854` · `SELECT … WHERE id_mensaje_turno_vencido=36222854` → `REPROGRAMACION \| E-MAIL \| t7.paso2@example.test \| enviado=S` · `id_turno_vencido=19999101` OTORGADO · CU POST `/turnos/vencidos/migrar` | dump 2026-09-22 | **verificado** |
| 4 | Golden `GENERA_MAILS.f_mail_reprogramacion_turno` / `f_sms_reprogramacion_turno` | test | `GeneraMailsReprogramacionEngineGoldenMasterTest` · REPLACE Oracle (null quita token) · Tests run: 2 Failures: 0 | Hospital-Api 2026-09-22 | **verificado** |
| 5 | Rollback a mitad (mensaje + archivo) | test | POST migrar **500** `fecha_hora_a_enviar` interval vs timestamp. Tras el 500: `19999101` sigue en `ts.turno`; `turno_vencido` 0; `mensaje_turno_vencido` 0. Reintento 200 tras el cast. | dump 2026-09-22 | **verificado** |
| 6 | Acceso denegado: POST sin JWT | endpoint | `POST http://localhost:8081/api/v1/turnos/avisos/despachar` sin Bearer → **401** | 2026-09-22 | **verificado** |
| 7 | p95 / COUNT volumen / dos actores mismo mensaje | no funcional | COUNT pendientes dump previo = **0** → **diferido(perf-volumen)**. p95 despacho ≈ 8,5 s vs ≤ 1,5 s → **diferido(perf-tiempo)**. Scheduler SKIP; no doble envío (Camino 1 no marca enviado en despacho) | dump 2026-09-22 | **verificado** |
| 8 | G6 operador | e2e | Francisco 2026-09-22 «ok si veo bien las consultas». `36222854` REPROGRAMACION; `19999101` en `turno_vencido`; ausente en `ts.turno` | operador | **verificado** |
