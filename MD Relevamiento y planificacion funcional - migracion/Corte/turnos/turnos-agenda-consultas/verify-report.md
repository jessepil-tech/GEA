---
title: Verify — T5.5 consulta agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas.verify
---

# Verify — Consulta Agenda

**Gate:** **PASS / gate-done** 2026-09-15 · Clarify **FIRME Camino 1** 2026-09-14 (opción 1). D-TUR-59 · D-TUR-60.  
G6 smoke ops **PASS** (Francisco: «ok lo veo bien a consultar agenda»).  
**No** cierra T5 padre ni T4 ni Excel. PDF valores: hijo [`turnos-agenda-consultas-pdf/`](../turnos-agenda-consultas-pdf/) **gate-done** 2026-09-15.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Turnero Consulta Agenda | **done** | xhtml L79 |
| North + Consultar | **done** | `consulta.xhtml` |
| Tabla rango `ts.turno` | **done** | JDBC equivalente `f_get_turnos_fecha` |
| Leyenda 5 estados fija | **done** | L200–206 facet footer (fuera del scroll) |
| Info turno | **done** (reusa T5) | L149 |
| Imprimir BIRT cable | **done** | GET sidecar `ConsultaAgenda` · TSK-app-g2-2 |
| Imprimir PDF con valores | **done** (hijo T5.5-pdf **gate-done**) | `turnos-agenda-consultas-pdf` |
| Excel POI | **done** | `turnos-agenda-consultas-export` |
| Equipo usable | **done** | [`turnos-agenda-consulta-equipo`](../turnos-agenda-consulta-equipo/) 2026-09-28 |
| Pre-agenda | **done** (hijo T5.6 **gate-done**) | `turnos-agenda-preagenda` |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Turnero abre vista | sí | **e2e-migrado** | `turnos-agenda.spec.ts` |
| Toast intervalo fechas | sí | **e2e-migrado** | mismo |
| Consultar filas | sí | **e2e-migrado** | fixture |
| Imprimir PDF | sí | **e2e-migrado** | stub blob CI (patrón T4). **G6 visual filas** = hijo `turnos-agenda-consultas-pdf` **gate-done** |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ G6. Universo de CU = esta matriz. e2e **PASS** 2026-09-14.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-14**. Gate UI **antes** de template.

| Control | Estado | Nota |
|---------|--------|------|
| geometría | **done** | accordion 170 + north filtros + tabla fill + south 120px; leyenda facet footer |
| Inventario copy | **done** | north / tabla / south / toast |
| Inventario validaciones | **done** | `WRONG_INTERVAL_DATE_3` / `_9` |
| Inventario interacción | **done** | Turnero + Consultar + infoTurno + Imprimir cable |
| Leyenda fija bajo scroll | **done** | HIS `facet footer`; no viaja con filas |
| Consultar centrado | **done** | pedido producto (HIS left; MarAuto como Agenda) |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Turnero abre vista Consulta Agenda | e2e | `turnos-agenda.spec.ts` | CI 2026-09-14 | **verificado** |
| 2 | Consultar filas `ts.turno` | e2e | G6 Francisco `/turnos/agenda` | 2026-09-15 «ok lo veo bien a consultar agenda» | **verificado** |
| 3 | Volver Agenda (sin calendario 260 en consulta) | e2e | mismo acto G6 | 2026-09-15 | **verificado** |
| 4 | Imprimir cable sidecar | e2e | GET stub blob CI + live T5.5 | patrón T4 | **verificado** |
| 5 | Acceso: perfil de menú Consulta Agenda | e2e | actor G6 con perfil; «sin el rol» no reabrir | T5.5 G6 | **verificado** |
| 6 | Volumen: filas de la tabla `ts.turno` en grilla | e2e | G6 Consultar hoy | 2026-09-15 | **verificado** |
| 7 | Imprimir BIRT cable; valores = hijo | e2e | sidecar `ConsultaAgenda`; blob `consulta-agenda-2026-09-15.pdf` | [`turnos-agenda-consultas-pdf`](../turnos-agenda-consultas-pdf/) | **verificado** |

## Smoke

**G6 stack real — PASS 2026-09-15** (ops Francisco, `/turnos/agenda` vista consulta):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Turnero Consulta Agenda → north HIS + tabla + leyenda fija + south Excel disabled / Imprimir | **PASS** |
| 2 | Consultar → filas `ts.turno` | **PASS** |
| 3 | Volver Agenda → calendario | **PASS** (cubierto en el acto; sin calendario 260 en consulta) |
| 4 | PDF con valores = grilla | **PASS** hijo `turnos-agenda-consultas-pdf` G6 2026-09-15 |

## Resultado

**PASS / gate-done T5.5** 2026-09-15. Hijo PDF [`turnos-agenda-consultas-pdf/`](../turnos-agenda-consultas-pdf/) **gate-done**. Excel [`turnos-agenda-consultas-export/`](../turnos-agenda-consultas-export/) **gate-done**. Accordion Pre-agenda: [`turnos-agenda-preagenda/`](../turnos-agenda-preagenda/) **gate-done**. T6 padre **diferido**. Equipo en [`turnos-agenda-consulta-equipo`](../turnos-agenda-consulta-equipo/).
