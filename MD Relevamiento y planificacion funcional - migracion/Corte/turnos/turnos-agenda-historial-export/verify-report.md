---
title: Verify — T6.3 hijo · Excel Historial Turnos
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial-export.verify
---

# Verify — Excel Historial Turnos

**Gate:** **PASS / gate-done** 2026-09-18 · Clarify **FIRME** Camino 1 D-TUR-73.  
G6 visual **PASS** (Francisco: «ok se ve bien el excel»).  
**No** cierra padre T6.3 ni D-TUR-17.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Cable GET + botón south | **done** | este hijo |
| `.xls` 14 cols título Historial Turnos | **done** | G6 2026-09-18 |
| Header filtros HIS=null | **WAIVE** | spec |
| Equipo usable | **diferido** | D-TUR-17 |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Stub blob `.xls` | sí | **e2e-migrado** | CI ≠ POI real |
| Valores reales | sí | **N/A** | G6 abrir archivo |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

**N/A** xhtml nuevo. South 150px padre; este corte habilita el acto.

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **N/A** | south 150px padre |
| copy | **N/A** | `Exportar Excel` ya en padre |
| validaciones | mismas que Consultar | `WRONG_INTERVAL_HOUR` |
| interacción | **N/A** | disparador south `Exportar Excel` |

## No funcionales

| Eje | Presupuesto | Estado |
|-----|-------------|--------|
| Tiempo | p95 = lista | G6: el xls salió en el click |
| Volumen | dump COUNT hist=17; **diferido(perf-volumen)** | G6 visual; no emparejó COUNT |
| Concurrencia | GET sin lock | **N/A** |

## Acceso

Mismos perfiles T5. Rol = SELECT hist. No muta `ts`. Auditoría N/A. Prueba negativa «sin el rol» = padre T5 (misma ruta `/turnos/agenda`).

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Handler 14 cols; vacío = xls; `WRONG_INTERVAL_HOUR`; headers null | test | `mvn -pl application -am "-Dtest=GetHistTurnoExcelQueryHandlerTest" "-Dsurefire.failIfNoSpecifiedTests=false" test` · Tests run: 3 Failures: 0 | Hospital-Api 2026-09-18 | **verificado** |
| 2 | GET `/api/v1/turnos/agenda/historial/exportar.xls` | endpoint | sin token **401** · localhost:8081 2026-09-18 | Resource `historialExportarXls` | **verificado** |
| 3 | e2e stub download `historial-turnos-YYYY-MM-DD.xls` | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Historial"` · 6 passed | Hospital-Web 2026-09-18 | **verificado** |
| 4 | G6 abre el `.xls` (título + 14 cols + fechas con hora) | e2e | Francisco Consultar → Exportar Excel | 2026-09-18 «ok se ve bien el excel» | **verificado** |
| 5 | Acceso: mismo perfil menú Agenda que T5; actor sin rol = padre | e2e | actor G6 con perfil; prueba negativa «sin el rol» = padre T5 | T5 / T6.3 verify | **verificado** |
| 6 | NFR GET; concurrencia N/A (sin FOR UPDATE); volumen **diferido(perf-volumen)** | test | no muta `ts`; G6 xls salió en el click; dump COUNT=17 no emparejado a UI | G6 2026-09-18 | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-18. Equipo usable = D-TUR-17. Padre T6.3 sigue abierto (popup G6 · NFR lista).

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, las filas vuelven a no verificado.
