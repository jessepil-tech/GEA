---
title: Verify — T6.2 hijo · PDF cola Reasignación
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-print.verify
---

# Verify — PDF cola Reasignación

**Gate:** **PASS / gate-done** 2026-09-17 · Clarify **FIRME** Camino 1 D-TUR-68.  
G6 visual **PASS** (Francisco: «listo ahi pude probar y sale info el reporte»).  
**No** cierra D-TUR-17, T6 padre, ni reabre T6.2 lista. Excel hijo **gate-done**.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Diseño sidecar `TurnosAReasignar` | **done** Reports | playbook 2026-09-08 |
| Cable GET + Imprimir south | **done** | este hijo · Api+Web |
| PDF con valores = grilla cola | **done** | G6 2026-09-17 |
| Excel POI | **done** | hijo [`turnos-agenda-cola-reasignar-export/`](../turnos-agenda-cola-reasignar-export/) **gate-done** |
| Equipo usable | **diferido** | D-TUR-17 |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Imprimir stub blob | sí (padre accordion) | **e2e-migrado** | CI stub ≠ BIRT |
| PDF con valores reales | sí | **N/A** | G6 visual **PASS** |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

**N/A** este hijo: no hay xhtml nuevo. Chrome Imprimir ya T6.2 (`turnosAReasignar.xhtml`).

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **N/A** | south 150px padre |
| copy | **N/A** | label Imprimir padre |
| validaciones | **N/A** | mismas fechas/horas que Consultar |
| interacción | **N/A** | disparador Imprimir del padre |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Diseño BIRT ya portado | PDF | `Hospital-Reports/src/main/resources/designs/TurnosAReasignar.rptdesign` · playbook `TurnosAReasignar-birt-docker.pdf` | Reports 2026-09-08 | **verificado** |
| 2 | Handler manda `0` si Todos; `reportId=TurnosAReasignar` | test | `mvn -pl application,core -am test -Dtest=GetColaReasignarPdfQueryHandlerTest,ColaReasignarValidationTest` · exit 0 | Hospital-Api 2026-09-17 | **verificado** |
| 3 | GET `cola-reasignar/imprimir.pdf` | endpoint | sin token **401**; G6 autenticado **200** PDF con filas | localhost:8081 2026-09-17 | **verificado** |
| 4 | e2e Imprimir enabled + stub download | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Cola reasignar T6.2"` · 5 passed | Hospital-Web 2026-09-17 | **verificado** |
| 5 | G6 PDF valores = grilla cola | PDF | Francisco Consultar → Imprimir; sidecar `TurnosAReasignar` con info | 2026-09-17 «listo ahi pude probar y sale info el reporte» | **verificado** |
| 6 | Acceso: mismo perfil menú Agenda que T6.2 | e2e | actor G6 con perfil; prueba negativa «sin el rol» = padre T5 | T6.2 verify | **verificado** |
| 7 | Volumen: 1 fila dump = package = PDF | test | `ts.turno_a_reasignar` hoy COUNT=1; `p_get_turnos_a_reasignar(..., 92001)` = 1 | dump `grupogea-hospital_dev` 2026-09-17 | **verificado** |
| 8 | NFR GET; concurrencia N/A (sin FOR UPDATE) | test | no escribe; G6 PDF salió en el click; p95 no instrumentado | G6 2026-09-17 | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-17. Excel hijo **gate-done**. Equipo usable = D-TUR-17.

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, filas 2–5 vuelven a no verificado.
