---
title: Verify — Turnos hab buscadores
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-hab-buscadores
---

# Verify — Buscadores hab turnos

**Gate:** **PASS / gate-done** 2026-08-28  
Evidencia: API smoke (agente) + UI smoke (ops / agente parcial hab-serv).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Buscador servicio–centro | **done** | RF-2 / RF-4 |
| Buscador personal–servicio | **done** | RF-3 / RF-4 |
| DDL puente `servicio_centro` / `personal_servicio` | **done** | RF-1 · Flyway V38/V39 |
| Sync `atiende_turnos` | diferido | D-TUR-11 |
| Flags amb/int / solo activos | diferido(`turnos-hab-buscadores-filtros`) | D-HAB-BUSC-01 |
| Combo centro / combos pers / cols doc | diferido(`turnos-hab-buscadores-filtros`) | gaps xhtml |
| Filtro admin turno | diferido | D-HAB-BUSC-02 |

## Paridad UI (xhtml) — corte padre v1

Fuente: `buscadorServicioCentro.xhtml` · `buscadorPersonalServicio.xhtml` · integración en `habTurnos*.xhtml`.  
Canon: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md).

| Control | Estado | Nota |
|---------|--------|------|
| Labels msg.* (Servicio, Centro, Profesional, Buscar, Aceptar, Cancelar) | **done** | |
| Entrada por nombre (no ID como primario) | **done** | |
| Lista resultados + paginator | **done** | |
| Selección → contexto hab + Agregar enabled | **done** | |
| Botones compactos (no full-width) | **done** | |
| Layout filtros (criterio diseñador) | **done** (v1) | Gaps combo/amb/int → hijo filtros |
| Filtros/cols legacy extras | diferido(`turnos-hab-buscadores-filtros`) | No silencio |

## Smoke

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Flyway V38/V39 | **PASS** — `flyway_schema_history` success |
| 2 | GET búsqueda sin Bearer | **PASS** — 401 |
| 3 | GET serv-centro `q=CLINICA` | **PASS** — 1001/10 CLINICA MEDICA / HOSPITAL-DEMO |
| 4 | GET pers-serv `q=ADMIN` | **PASS** — 90001 ADMIN, Demo |
| 5 | UI hab-serv buscador → lista hab | **PASS** — labels + Agregar enabled + filas |
| 6 | UI hab-pers idem | **PASS** — confirmado ops |
| 7 | Regresión T2 (contexto previo) | OK — hab list tras selección |

## Resultado

**gate-done.** Hijo pendiente: [`turnos-hab-buscadores-filtros/`](../turnos-hab-buscadores-filtros/).  
Siguiente programa tipico: Clarify del hijo filtros **o** T3 `turnos-horarios-grupos`.

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (dialog buscador) |
| Decisión | **cerrado-pre-regla** |
| Viaje (pasos) | smoke 2026-08-28 (API + UI hab-serv/pers) |
| Fixture | CLINICA / ADMIN seed |
| Legacy e2e | no |
