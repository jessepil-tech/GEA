---
title: Plan — T6.4 · suspender / quitar suspensión de grilla
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender
---

# Plan — Suspender / quitar suspensión de grilla

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Ninguno. Tablas en dump. |
| API | Query lista candidatos (JDBC). Command suspender / quitar con ids + motivo + horas (port SP, golden master). Resource delgado. |
| Web | Dos páginas familia T4. Radio serv/pers; equipo disabled. Reuso buscadores T2/T4. Popup motivo. Menú TURNOS / Agenda turnos. |
| Tests | Golden: vacío, motivo null, split, otorgado parcial, dos actores. e2e 4 viajes. G6 acto T4. |

Orden: Clarify **FIRME** → G0 inventarios (borrador listo) → Gate UI **antes** de API-first → G2 Api → G4 Web → G5 e2e → G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | FIRME + inventarios + COUNT `turno` del rango G6 |
| G1 | N/A Flyway |
| G2 | Api lista + suspender + quitar + golden + IT rollback |
| G3 | N/A reportes |
| G4 | Dos páginas + menú; equipo disabled |
| G5 | Playwright e2e-migrado |
| G6 | Francisco: ve `SUSPENDIDO` y quitar vuelve `LIBRE` |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Techo 7/8 con buscadores | Buscadores = reuso T4, no port nuevo; si G0 crece → partir quitar |
| Índice 0 firmas PL/SQL | SoT leído a mano; golden contra oráculo JDBC |
| SMS/mail dentro del SP | No portar inserts `envio_sms` / `mensaje_whatsapp` / `p_genera_mail_*` en v1 (T7); documentar gap verify |
| `pp_libera_turno` ya en T5 | Reusar; no duplicar rarezas |
| Auditoría TBL_AUD | Chequear al implementar; si falta → `diferido(auditoria)` |
| BODY TURNOS vs e2e-mostrador | Mostrador **no** porta BODY; este corte sí. Un owner. |
