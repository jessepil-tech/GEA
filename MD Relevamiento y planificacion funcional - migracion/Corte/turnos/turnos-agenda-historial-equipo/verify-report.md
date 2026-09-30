---
title: Verify — Equipo en Historial de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-historial-equipo.verify
---

# Verify — Equipo en Historial de Agenda

Combo cableado. Viaje mock, lectura `9676753` y p95 medidos. Volumen en vacío.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Combo Equipo en Historial de Agenda | done | spec |
| Consultar `hist_turno` por equipo | done | spec |
| Menú `consultaHistorialTurno` | **N/A** | otra hoja, fuera de T6.3 |
| Jobs `historial` | **N/A** | el corte padre no encontró jobs de esta hoja |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Techo de `historialTurno.xhtml` | test | `indice-legacy.sh --semilla asignacionTurnos/historialTurno.xhtml` 2026-09-28: 2/8 xhtml, 5/15 beans, 0 firmas, 3/5 rptdesign. Entra solo el combo | 2026-09-28 | verificado |
| 2 | Viaje elegir equipo y ver el hist | e2e | `npx playwright test e2e/turnos-agenda-historial-equipo.spec.ts --workers=1` → 1 passed. Mock. No es el G6 | 2026-09-28 | verificado |
| 3 | Consultar lee `9676753` | endpoint | `GET /api/v1/turnos/agenda/historial?fechaDesde=2026-09-28&fechaHasta=2026-09-28&idCentroAte=1001&codItemEquipo=EQDEMO1001` → **200**. 2 filas. `idHistTurno=9676753`, `personalEquipo=EQUIPO DEMO`, `equipo=EQUIPO DEMO` | 2026-09-28 | verificado |
| 4 | Acceso: perfil sin el rol de menú | acceso | `npx tsx --tsconfig tsconfig.app.json e2e/acceso-historial-equipo.check.ts` → `ok acceso historial-equipo`. Perfil RECEPCION sin el rol de menú no ve `nav-turnos-agenda`. ATENCION_TURNOS sí. La hoja es el acordeón de esa ruta | 2026-09-28 | verificado |
| 5 | p95 de Consultar con equipo | no funcional | 20 GET del mismo filtro: p95 **293 ms** (índice `ceil(0.95*20)-1`, presupuesto ≤ 2 s). Volumen = 2 filas de `hist_turno` ese día → **medido en vacío** + **diferido(perf-volumen)**. Concurrencia **N/A** (SELECT sin `FOR UPDATE`) | 2026-09-28 | verificado |
| 6 | G6 columna Equipo al filtrar | G6 | ealbo, 2026-09-28: «ahi pude probar y sale el valor del equipo» | 2026-09-28 | verificado |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | e2e-migrado |
| Viaje | Historial → centro `HOSPITAL-DEMO` → Equipo `EQUIPO DEMO` → hist `9676753` |
| Spec | `Hospital-Web/e2e/turnos-agenda-historial-equipo.spec.ts` |
| Fixture | PG `id_hist_turno=9676753` |
| Legacy e2e | no |
