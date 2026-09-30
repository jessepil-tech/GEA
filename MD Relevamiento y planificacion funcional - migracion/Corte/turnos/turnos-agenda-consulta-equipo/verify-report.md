---
title: Verify — Equipo en Consulta de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-consulta-equipo.verify
---

# Verify — Equipo en Consulta de Agenda

**Gate:** **PASS / gate-done** 2026-09-28.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| el combo Equipo de la consulta | done | spec |
| Consultar con `codItemEquipo` | done | spec |
| Jobs | **N/A** | el padre |

## Paridad UI (xhtml)

| Control | Estado | Nota |
|---------|--------|------|
| geometría | verificado | misma celda |
| copy | verificado | inventario |
| validaciones | verificado | inventario |
| interacción | verificado | combo habilitado |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Viaje: consulta muestra el combo habilitado | e2e | `npx playwright test e2e/turnos-agenda-equipo-hermanas.spec.ts --workers=1` → 4 passed. Mock. No es el G6 | 2026-09-28 | verificado |
| 2 | Consulta con equipo | endpoint | `GET /api/v1/turnos/agenda/consulta?fechaDesde=2026-09-28&fechaHasta=2026-09-28&idCentroAte=1001&idServicio=10&codItemEquipo=EQDEMO1001` → **200**. 2 filas | 2026-09-28 | verificado |
| 3 | Acceso: perfil sin el rol de menú | acceso | `npx tsx --tsconfig tsconfig.app.json e2e/acceso-agenda-equipo-hermanas.check.ts` → `ok acceso agenda-equipo-hermanas`. RECEPCION no ve `nav-turnos-agenda`. ATENCION_TURNOS sí. Rol funcional: ninguno en estas hojas | 2026-09-28 | verificado |
| 4 | p95 | no funcional | 20 repeticiones: p95 **668 ms** (presupuesto ≤ 2 s). Volumen = 2 filas → **medido en vacío** + **diferido(perf-volumen)**. Concurrencia **N/A** | 2026-09-28 | verificado |
| 5 | G6 | G6 | ealbo, 2026-09-28: «si esas 4 pantallas probe el flujo y funciona» | 2026-09-28 | verificado |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | e2e-migrado |
| Viaje | consulta muestra el combo habilitado |
| Spec | `Hospital-Web/e2e/turnos-agenda-equipo-hermanas.spec.ts` |
| Fixture | `EQDEMO1001` |
| Legacy e2e | no |

## Resultado

**PASS / gate-done** 2026-09-28. `diferido(perf-volumen)`.
