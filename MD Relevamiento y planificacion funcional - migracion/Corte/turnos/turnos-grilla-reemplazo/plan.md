---
title: Plan — T6.5 · reemplazo profesional de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo
---

# Plan — Reemplazo profesional de grilla

## Enfoque

| Capa | Decisión (Camino 1 FIRME) |
|------|-------------------------------|
| DDL | Ninguno. Tablas en dump. |
| API | Query lista candidatos (JDBC). Command reemplazar/quitar con ids + motivo + horas (port SP, golden master). Resource delgado. |
| Web | Una página familia T4. Dos buscadores personal (reuso). Motivo north. Confirm modal DS. Leyenda anclada v1.19. Menú TURNOS / Agenda turnos. |
| Tests | Golden: vacío, motivo null, split, otorgado parcial, solape, dos actores. e2e 4 viajes. G6 acto T4. |

Orden: Clarify **FIRME** → G0 inventarios (borrador listo) → Gate UI **antes** de API-first → G2 Api → G4 Web → G5 e2e → G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | FIRME + inventarios + COUNT `turno` del rango G6 |
| G1 | N/A Flyway |
| G2 | Api lista + reemplazar + quitar + golden + IT rollback |
| G3 | N/A reportes |
| G4 | Página + menú; footer v1.19 |
| G5 | Playwright e2e-migrado |
| G6 | Francisco: ve `personal_reemplazado='S'` y quitar vuelve nulos |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Índice 0 firmas PL/SQL | SoT leído a mano; golden contra oráculo JDBC |
| Reemplazante sin HAB en el servicio | `diferido(fixture)` declarado; no seed |
| Solape sin `FOR UPDATE` | last-write o ORA-20001 observable; test dos actores |
| Auditoría TBL_AUD | `diferido(auditoria)` desde el arranque |
| BODY TURNOS vs e2e-mostrador | Mostrador **no** porta BODY; este corte sí. Un owner. |
| Confirm `window.confirm` HIS | Modal DS, copy HIS (canon feedback UX) |
