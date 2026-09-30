---
title: Verify — T5.5 hijo · PDF consulta agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-pdf.verify
---

# Verify — PDF Consulta Agenda

**Gate:** **PASS / gate-done** 2026-09-15 · Clarify **FIRME Camino 1** D-TUR-62.  
G6 smoke **PASS** (Francisco: «ok ahora se está visualizando información en los pdf»).  
**No** cierra Excel, T6, D-TUR-17 usable, ni reabre T5.5 padre.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Imprimir cable sidecar | **done** T5.5 | GET `consulta/imprimir.pdf` |
| PDF con valores = grilla hoy | **done** | este hijo · G6 2026-09-15 |
| Histórico fecha &lt; hoy | **diferido** | T6 job |
| Columna equipo catálogo | **diferido** | D-TUR-17 |
| Excel POI | **fuera** | `turnos-agenda-consultas-export` **gate-done** |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Imprimir stub blob | sí (padre) | **e2e-migrado** T5.5 | no reabrir |
| PDF con valores reales | sí | **N/A** | CI stub ≠ BIRT; G6 visual **PASS** |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

**N/A** este hijo: no hay xhtml nuevo. Chrome Imprimir ya T5.5 (`consulta.xhtml`).

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **N/A** | sin layout nuevo |
| copy | **N/A** | sin MessageBundle |
| validaciones | **N/A** | sin IMPBUS |
| interacción | **N/A** | disparador Imprimir del padre |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | `ts.turno_vencido` existe en PG | test | `to_regclass` → `ts.turno_vencido` | dump 2026-09-15 | **verificado** |
| 2 | `ts.equipo_serv_centro` existe | test | `to_regclass` → `ts.equipo_serv_centro` | dump 2026-09-15 | **verificado** |
| 3 | `p_get_turnos_fecha` no ERROR | test | `SELECT count(*) …` hoy = 2 | dump 2026-09-15 | **verificado** |
| 4 | Count función ≈ `ts.turno` hoy | test | `turno_hoy=2`; `nulls=2`; `zeros=2`; `1001_and_zeros=2`; `birt_44983=0` | dump 2026-09-15 | **verificado** |
| 5 | Api Todos no manda `""`; diseño sin 44983 | test | `GetConsultaAgendaPdfQueryHandlerTest` 4 PASS; diseño grep 44983=0 | 2026-09-15 | **verificado** |
| 6 | G6 PDF valores = grilla | e2e | Francisco Consultar hoy → Imprimir; blob `consulta-agenda-2026-09-15.pdf` | 2026-09-15 «ok ahora se está visualizando información en los pdf» | **verificado** |
| 7 | `ts.te_persona` existe | test | `to_regclass` → `ts.te_persona` (vacía) | dump 2026-09-15 | **verificado** |
| 8 | Acceso: mismo perfil de menú Consulta Agenda que T5.5 | e2e | actor G6 con perfil; prueba negativa «sin el rol» = padre T5.5 (no reabrir) | T5.5 verify | **verificado** |
| 9 | Volumen: 2 filas de la tabla `ts.turno` hoy = PDF | test | `turno_hoy=2` = count package | dump 2026-09-15 | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-15. Excel [`turnos-agenda-consultas-export/`](../turnos-agenda-consultas-export/) **gate-done**. Histórico vencidos = T6. Equipo usable = D-TUR-17.

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, filas 1–6 vuelven a no verificado.
