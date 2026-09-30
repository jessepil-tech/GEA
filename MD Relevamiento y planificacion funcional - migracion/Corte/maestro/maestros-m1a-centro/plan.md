---
title: Plan — M1a ABM centro de atención
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1a-centro.plan
---

# Plan — M1a

## Enfoque

Gate UI (inventarios de este slug) → Api CQRS sobre `ts.centro_atencion` (dump) → page Web paritaria → verify con id de fila. **Sin Flyway.** Padres grp/provincia/localidad = `psql` del seed `Hospital-Api/infrastructure/src/main/resources/db/dev-seed/ts_maestros_m1a_centro_padres.sql`.

## Capas Api

`core` (puerto) → `application` (command/query + dispatcher) → `infrastructure` (JDBC) → `presentation-api` (resource delgado). Sin SQL en el Resource.

## Capas Web

`application/use-cases` + page bajo configuración. Sin `HttpClient` de negocio en el component. Labels `*-labels.ts` desde inventario copy.

## Cortes internos (cuando se codee)

| # | Qué | Stop |
|---|-----|------|
| G0 | Inventarios (este slug, hechos al abrir) | — |
| G1 | Api list/get/create/update/delete + IT | sin id en PG = no cierre |
| G2 | UI shell + datos + buscador | sin geometría = no smoke |
| G3 | Verify ledger + actor sin menú | — |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| PG de trabajo ya tiene centros demo | Evidencia = fila **nueva** del CU, no un id seed |
| `AUD_CENTRO_ATENCION` | `diferido(auditoria)` escrito |
| NextId vs filas 99001 del seed | Seed usa 99001–99003; NextId no debe chocar (documentar rango) |
| Techo west | ya partido a `maestros-m1a-centro-west` |
