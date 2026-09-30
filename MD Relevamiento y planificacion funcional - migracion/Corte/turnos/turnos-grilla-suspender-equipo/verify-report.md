---
title: Verify — Equipo en Suspender y Quitar suspensión
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-grilla-suspender-equipo.verify
---

# Verify — Equipo en Suspender y Quitar suspensión

**Gate:** **PASS / gate-done** 2026-09-28.

Radio cableado. Viaje mock, escritura `17255287` y p95 medidos. Volumen en vacío.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Radio Equipo en Suspender | done | spec |
| Radio Equipo en Quitar suspensión | done | spec |
| Consultar candidatos por equipo | done | spec |
| Suspender y quitar un turno de equipo | done | spec |
| Jobs de estas hojas | **N/A** | el padre no encontró jobs |
| Mail/SMS, reemplazo, cola, consulta, sobreturno, múltiples | **diferido** | hermanas y cortes ya firmados |

## Paridad UI (xhtml)

| Control | Estado | Nota |
|---------|--------|------|
| geometría | verificado | misma celda del radio; no se redibuja |
| copy | verificado | inventario |
| validaciones | verificado | «Debe seleccionar un equipo.» |
| interacción | verificado | radio habilita el combo |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Viaje: radio habilitado y combo visible | e2e | `npx playwright test e2e/grilla-turnos-suspender-equipo.spec.ts --workers=1` → 2 passed. Mock. No es el G6 | 2026-09-28 | verificado |
| 2 | Consultar por equipo | endpoint | `GET /api/v1/turnos/grilla/candidatos-suspender?idCentroAte=1001&idServicio=10&fechaDesde=2026-09-28&fechaHasta=2026-09-28&codItemEquipo=EQDEMO1001` → **200**. 2 filas | 2026-09-28 | verificado |
| 3 | Suspender y quitar dejan la fila | escritura | `id_turno=17255287` · lote `ts.suspension_agenda_turnos` **id=6** `ctd_turnos=1` `actualizado_por=admin` 15:26:42 · `hist_turno` `9676773` `SUSPENDIDO` · `9676774` `LIBRE` 15:27:17 · `SELECT estado_turno, id_motivo_suspende FROM ts.turno WHERE id_turno=17255287` → `LIBRE`, motivo nulo. CU Suspender Agenda Turnos y Quitar Suspensión Agenda | 2026-09-28 | verificado |
| 4 | Acceso: perfil sin el rol de menú | acceso | `npx tsx --tsconfig tsconfig.app.json e2e/acceso-suspender-equipo.check.ts` → `ok acceso suspender-equipo`. RECEPCION no ve las dos hojas. ATENCION_TURNOS sí. Rol funcional: ninguno, como el padre | 2026-09-28 | verificado |
| 5 | p95 de Consultar con equipo | no funcional | 20 GET del mismo filtro: p95 **726 ms** (índice `ceil(0.95*20)-1`, presupuesto ≤ 2 s). Volumen = 2 filas ese día → **medido en vacío** + **diferido(perf-volumen)**. Concurrencia **N/A**: el padre firmó que este SP no usa `FOR UPDATE` | 2026-09-28 | verificado |
| 6 | G6 radio Equipo en las dos hojas | G6 | ealbo, 2026-09-28: «si esas en suspender agendar y quitar lo probe y funciono» | 2026-09-28 | verificado |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | e2e-migrado |
| Viaje | Suspender y Quitar → radio Equipo → combo visible |
| Spec | `Hospital-Web/e2e/grilla-turnos-suspender-equipo.spec.ts` |
| Fixture | PG `id_turno=17255287` |
| Legacy e2e | no |

## Resultado

**PASS / gate-done** 2026-09-28. `diferido(perf-volumen)`. `diferido(auditoria)` sigue en el padre.
