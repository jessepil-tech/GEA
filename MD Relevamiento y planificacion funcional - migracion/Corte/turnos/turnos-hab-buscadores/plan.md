---
title: Plan — Turnos hab buscadores
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-hab-buscadores
---

# Plan — Buscadores hab turnos

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Flyway Api: `servicio_centro`, `personal_servicio` desde inventario `ts` (paridad nombres). Seed DEV. **No** inventar `*_agi`. |
| Core | Port búsqueda (query objects) — sin mezclar con `TurnosHabPort` mutaciones |
| Application | Queries list/search paged |
| Infrastructure | JDBC `ts.*` LIKE / unaccent según patrón existente en Api |
| Presentation | `GET /api/v1/turnos/buscadores/servicio-centro` · `…/personal-servicio` (o bajo `/configuracion/…` — decidir en B1; preferir turnos por consumidor T2) |
| Web | Componente(s) dialog en `shared` o `configuracion`; cablear hab-serv / hab-pers |

Orden: Clarify firma → B0 DDL/seed → B1 API → B2 UI dialog → B3 wire hab → B4 verify.

## Cortes

| Corte | Exit |
|-------|------|
| B0 | Clarify firmado + Flyway puente + seed |
| B1 | GET búsqueda serv-centro + pers-serv + IT 401/lista |
| B2 | Dialog UI + labels (Paridad UI) |
| B3 | Hab serv/pers usan buscador (IDs dejan de ser entrada primaria) |
| B4 | Smoke + verify + links matriz/pendientes |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Columnas puente incompletas en inventario local | Usar catálogo PG migrado / `pg_ts_columns`; Clarify bloquea si falta evidencia |
| Scope creep ABM vínculos | Solo SELECT + seed; ABM = otro slug |
| Confundir con D-TUR-11 | Sync `atiende_turnos` explícitamente fuera |
| Buscador “genérico hospital” | API/UI naces aquí; adopción otras pantallas = tickets aparte |

## Dependencias

- T2 gate-done (hab API/UI)
- T1 personal / centro / servicio en `ts`
- Firma Clarify #1–#3

## Fuera del plan de código

T3; D-TUR-11; D-TUR-12; amb/int avanzado.
