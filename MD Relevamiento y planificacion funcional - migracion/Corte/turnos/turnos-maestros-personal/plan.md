---
title: Plan — T1 Turnos maestros identidad
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-maestros-personal
---

# Plan — T1

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Nueva versión Flyway Api: extraer columnas del inventario / `pg_dump --schema-only` del PG migrado — **no** rediseñar. Schema `ts`. |
| Core | Port (p.ej. `TurnosIdentidadPort`) + excepciones de dominio (sin call center / sin personal) |
| Application | Queries CQRS: personal por login, call centers por `id_personal` |
| Infrastructure | `Jdbc*Adapter` sobre `ts.*`; seed `db/dev-seed/` alineado a ITs |
| Presentation | Resource `/api/v1/turnos/...` (prefijo claro, no mezclar con `/agi`) |
| Web | Use case + page picker; `hospital-menu.catalog.ts`: hoja TURNOS → `/turnos/inicio` |
| Identity | Sin cambio de contrato salvo que haga falta claim extra — **evitar**; D-TUR-01 usa username |

Orden de merge: Flyway + seed → API/IT → UI. No UI sin RF-5.

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| `persona` vs `personal` | NFR-2; review de nombres en SQL |
| Seed turno AGI con `id_personal` huérfano | RF-3: no forzar FK turno hasta alinear seed |
| Username Identity ≠ `login_name` legacy | Seed DEV con `login_name` = usuario smoke conocido; documentar en verify |
| PERSONAL 13 cols vs expectativa “ficha rica” | Es el canónico del inventario; ficha persona/otro vínculo es otro corte |
| Scope creep ABM | Rechazar PRs de forms de alta personal en este slug |

## Dependencias

- Identity oleada A (JWT) — **hecho**
- `ts.centro_atencion` / `ts.servicio` (V28/V30) — para FK de `personal_adm_tur_serv_centro` si se activan
- Relevamiento T0 — **hecho**

## Fuera del plan de código T1

T2+ (`turnos-config-hab-horarios`, etc.).
