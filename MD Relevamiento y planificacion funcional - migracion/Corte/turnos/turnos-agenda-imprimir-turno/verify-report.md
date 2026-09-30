---
title: Verify — T5 hijo · PDF turno (Agenda Imprimir)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-agenda-imprimir-turno.verify
---

# Verify — PDF turno Agenda

**Gate:** **PASS / gate-done** 2026-09-22 · Clarify **FIRME** Camino 1 D-TUR-77.  
G6 visual **PASS** (Francisco: «joya sale bien el pdf»).  
**No** cierra `f_imprime` coseguro, `TurnoTicket`, ficha west, ni pack logos.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Diseño sidecar `Turno` | **done** Reports (ya cobrado) | playbook · example `Turno.json` |
| Cable GET + Imprimir gear | **done** | este hijo · Api+Web |
| PDF con datos del turno | **done** | G6 2026-09-22 |
| `f_imprime_turno_pac` coseguro | **diferido** | `turnos-agenda-imprimir-turno-cose` |
| Imprimir ficha oeste | **diferido** | `turnos-agenda-ficha-imprimir` |
| `TurnoTicket` / matricial | **N/A** | recepción |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Imprimir stub blob | sí (padre agenda) | **e2e-migrado** | CI stub ≠ BIRT |
| PDF con valores reales | sí | **N/A** | G6 visual **PASS** |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

**N/A** este hijo: no hay xhtml nuevo. Chrome gear ya T5 (`agenda.xhtml`). Se añade el ítem Imprimir.

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **N/A** | menú gear padre |
| copy | **N/A** | label Imprimir ya en labels T5 |
| validaciones | **done** | disabled sin paciente / `RESERVADO` |
| interacción | **done** | disparador Imprimir del gear |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Diseño BIRT ya portado | PDF | `Hospital-Reports/.../Turno.rptdesign` · `examples/api-requests/Turno.json` | Reports | **verificado** |
| 2 | Handler manda `reportId=Turno` + params Camino 1 | test | `mvn -pl application -am test -Dtest=GetImprimirTurnoPdfQueryHandlerTest` · 3 tests · exit 0 | Hospital-Api 2026-09-22 | **verificado** |
| 3 | GET `{id}/imprimir.pdf` 401 sin token | endpoint | `GET http://localhost:8081/api/v1/turnos/agenda/40001/imprimir.pdf` → **401** | live Api 2026-09-22 | **verificado** |
| 4 | e2e Imprimir enabled + stub download | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Imprimir gear"` · 1 passed | Hospital-Web 2026-09-22 | **verificado** |
| 5 | G6 PDF valores del OTORGADO | PDF | Francisco gear Agenda → Imprimir; sidecar `Turno` con info | 2026-09-22 «joya sale bien el pdf» | **verificado** |
| 6 | Acceso: mismo perfil menú Agenda que T5 | e2e | actor G6 con perfil; prueba negativa «sin el rol» = padre T5 | T5 verify | **verificado** |
| 7 | Volumen: 1 turno de agenda = PDF no vacío | test | G6 visual 1 OTORGADO; no seed de `turno` | dump piloto 2026-09-22 | **verificado** |
| 8 | NFR GET; concurrencia N/A (sin FOR UPDATE) | test | no escribe; G6 PDF salió en el click; p95 no instrumentado | G6 2026-09-22 | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-22. Coseguro SP = `turnos-agenda-imprimir-turno-cose`. Ficha oeste = `turnos-agenda-ficha-imprimir`.

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, filas 2–5 vuelven a no verificado.
