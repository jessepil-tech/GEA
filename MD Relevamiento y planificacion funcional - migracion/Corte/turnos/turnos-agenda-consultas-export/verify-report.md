---
title: Verify — T5.5 hijo · Excel consulta agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export.verify
---

# Verify — Excel Consulta Agenda

**Gate:** **PASS / gate-done** 2026-09-16 · Clarify **FIRME Camino 1** D-TUR-63.  
G6 smoke **PASS** (Francisco: «ok el excel esta saliendo bien»).  
**No** cierra T5.5 padre, PDF, T6, D-TUR-17 ni Excel de otras pantallas.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Exportar Excel Consulta Agenda | **done** | este hijo · G6 2026-09-16 |
| Imprimir PDF | **fuera** | padre / pdf |
| Equipo header | **diferido** | D-TUR-17 |
| Excel otras pantallas | **fuera** | — |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Exportar Excel blob | sí | **e2e-migrado** | stub CI `.xls`; G6 archivo real **PASS** |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

No hay xhtml nuevo. Chrome south T5.5; este corte **habilita** el acto.

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **N/A layout** · south 120px ya padre | [inventario-geometria.md](inventario-geometria.md) |
| copy | **hecho** | [inventario-copy-msg.md](inventario-copy-msg.md) |
| validaciones | **hecho** | [inventario-validaciones.md](inventario-validaciones.md) |
| interacción | **hecho** | [inventario-interaccion-ui.md](inventario-interaccion-ui.md) |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | GET `…/consulta/exportar.xls` 200 + OLE HSSF | endpoint | método+ruta+status 200; G6 abrió el `.xls` | 2026-09-16 | **verificado** |
| 2 | 0 filas → handler empty / Web silencio (HIS noop) | test | `GetConsultaAgendaExcelQueryHandlerTest.listaVacia_noLlamaExcel` · Tests run: 2 Failures: 0 (2026-09-16) | HIS L345 | **verificado** |
| 3 | Handler 16 cols + POI OLE magic | test | `mvn -pl application,infrastructure -am -Dtest=GetConsultaAgendaExcelQueryHandlerTest,PoiHssfExcelAdapterTest test` · Tests run: 2+1 Failures: 0 (2026-09-16) | — | **verificado** |
| 4 | e2e botón enabled + download `.xls` | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Consulta Agenda"` · 5 passed (2026-09-16) | stub CI | **verificado** |
| 5 | G6 Excel valores = grilla hoy | e2e | Francisco Consultar hoy → Exportar Excel; frase operador | 2026-09-16 «ok el excel esta saliendo bien» | **verificado** |
| 6 | Acceso: mismo call center T5.5; actor sin el rol = padre (rol funcional del padre; no Raise en `generarReporteExcel`) | e2e | actor G6 con perfil; prueba negativa «sin el rol» = padre T5.5 (no reabrir) | T5.5 verify | **verificado** |
| 7 | Volumen: filas xls = grilla `ts.turno` hoy (orden del dump piloto) | e2e | G6 visual xls = grilla (misma fila 5) | 2026-09-16 | **verificado** |
| 8 | Concurrencia N/A (solo lectura; HIS sin FOR UPDATE en este acto) | test | spec NFR D-TUR-63 | D-TUR-63 | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-16. T6 padre **diferido**. Equipo usable = D-TUR-17. Excel de otras pantallas HIS = fuera.

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, las filas vuelven a no verificado.
