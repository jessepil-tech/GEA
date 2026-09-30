---
title: Verify — Combo Equipo en Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-equipo.verify
---

# Verify — Combo Equipo en Agenda

`verificar-sdd.sh turnos-agenda-equipo` → FAIL 0 · WARN 0. Seis filas verificadas. Volumen **diferido(perf-volumen)** y auditoría **diferido(auditoria)**. **PASS / gate-done** 2026-09-28.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Combo Equipo en el north de Agenda | done | ledger 2 |
| Ver el hueco EQUIPO y otorgarlo | done | ledger 3 y 6 |
| Historial, suspender, cola, consulta, sobreturno, múltiples | **diferido** | [`turnos-agenda-equipo-hermanas`](../turnos-agenda-equipo-hermanas/) |
| Reportes del ticket | **N/A** | hijos de impresión ya cerrados |
| Jobs `agenda` | **N/A** | sin coincidencias |
| Auditoría campo a campo de `ts.turno` | **diferido(auditoria)** | `TBL_AUD_TURNO` |
| Volumen | **diferido(perf-volumen)** | un equipo, un hueco |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Techo de `agenda.xhtml` | test | `indice-legacy.sh --semilla asignacionTurnos/agenda.xhtml` 2026-09-24: 3/8 xhtml, 4/15 beans, 0 firmas, 3/5 rptdesign | 2026-09-24 | verificado |
| 2 | Viaje elegir equipo y ver el hueco | e2e | `npx playwright test e2e/turnos-agenda-equipo.spec.ts --workers=1` → 1 passed. Mock. No es el G6 | 2026-09-28 | verificado |
| 3 | Otorgar escribe el hueco | escritura | `id=17255287` OTORGADO, paciente `20001`, `2026-09-28 08:00`, `EQDEMO1001`, `actualizado_por=admin`. Hist `9676753` `2026-09-28 10:24:25`. CU combo Agenda | 2026-09-28 | verificado |
| 4 | Acceso: actor sin el menú de Agenda | acceso | `npx tsx --tsconfig tsconfig.app.json e2e/acceso-agenda-equipo.check.ts` → `ok acceso agenda-equipo`. Perfil RECEPCION sin el rol de menú no ve `nav-turnos-agenda`. ATENCION_TURNOS sí | 2026-09-28 | verificado |
| 5 | p95, volumen y dos otorgar del mismo turno | no funcional | 20 GET grilla `2026-10-01` + `EQDEMO1001`: p95 **588 ms** (presupuesto default de grilla ≤ 2 s). Volumen del día = 1 fila en `ts.turno` (la grilla muestra 12 por el tajo) → **medido en vacío** + **diferido(perf-volumen)**. Dos POST `/otorgar` paralelos sobre `17255288` (estrategia T5, `FOR UPDATE` ya existente): A **200** hist `9676754`; B **404** «No se pudo recuperar el turno nro. 17255288». Un solo OTORGADO | 2026-09-28 | verificado |
| 6 | G6 otorgar por equipo | G6 | ealbo, 2026-09-28: «pude asignar turno por equipo» | 2026-09-28 | verificado |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | e2e-migrado |
| Viaje | Combo Equipo → grilla con nombre `EQUIPO DEMO` |
| Spec | `Hospital-Web/e2e/turnos-agenda-equipo.spec.ts` |
| Fixture | mock `EQDEMO1001` / `17255287`. PG real: ese id OTORGADO el 2026-09-28 08:00 |
| Legacy e2e | no |
