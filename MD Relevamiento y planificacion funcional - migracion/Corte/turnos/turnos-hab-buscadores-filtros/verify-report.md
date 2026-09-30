---
title: Verify — Turnos hab buscadores filtros
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-hab-buscadores-filtros
---

# Verify — filtros buscadores

**Gate:** **PASS / gate-done** 2026-08-28 (ops: UI OK tras filtros + fix reabrir).

## Capacidades

| Capacidad | Estado |
|-----------|--------|
| Combo centro (serv) | **done** |
| Filtro + col amb/int | **done** |
| Apellido/nombre + combos + cols doc (pers) | **done** |
| personal_adm combos/búsqueda | **done** (seed V40; fallback si sin adm) |
| Reabrir Buscar sin mezclar display→apellido | **done** |
| Multi-select | N/A hab |
| GET combo sin 404 global | **done** (interceptor) |

## Smoke

| # | Paso | Resultado |
|---|------|-----------|
| 1–6 | Combos, amb/int, pers cols, selección hab | **PASS** (ops) |
| 7 | Reabrir limpio tras selección | **PASS** |

## Reglas anti-regresión

Canon actualizado: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.2 ·  
Web: `hospital-web-buscadores.mdc`.

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **cerrado-pre-regla** |
| Viaje (pasos) | smoke ops 2026-08-28 |
| Fixture | — |
| Legacy e2e | no |
