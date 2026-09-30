---
title: SDD — T6.2 hijo · Excel cola Reasignación
description: >-
  South Exportar Excel de turnosAReasignar.xhtml: POI HSSF desde JDBC cola.
  No sidecar Reports.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-export
---

# turnos-agenda-cola-reasignar-export

Padre: [`turnos-agenda-cola-reasignar/`](../turnos-agenda-cola-reasignar/) T6.2 **gate-done**.  
PDF: [`turnos-agenda-cola-reasignar-print/`](../turnos-agenda-cola-reasignar-print/) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Clarify **FIRME Camino 1** D-TUR-69. Francisco: «ok siguiente trabajo seria el excel».  
Instalación de referencia: Call Center Demo (misma T6.2). Sin ramas `esClienteX()` en `actionBtnExportarExcel`.  
**No WAIVE:** HIS `BBTurnosAReasignar.actionBtnExportarExcel` + `XLSParser` HSSF.  
**No** sidecar. **No** SheetJS. **No** es el Excel de Consulta Agenda.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (Java `XLSParser`; JDBC cola ya T6.2) | n/a |
| Rango Flyway | **n/a** (solo lectura) | — |
| Tablas `ts` que escribe | ninguna | n/a |
| Rama | `dev/t62-cola-reasignar` | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Fila cola | no | T4 CU | G6 print: 1 fila hoy en `_dev` |
| Combos | no | T5 | disponible |
| Equipo usable | no | D-TUR-17 | header Equipo = `<TODOS>` |
| `mail_persona` | no | columna presente, valor vacío | igual T5.5-excel |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrar; Clarify **FIRME**) |
| Reservas | BODY n/a · Flyway n/a · sin escritura |
| Universo firmado | [spec.md](spec.md) — **FIRME** Camino 1 |
| Fixture | misma cola que Consultar/Imprimir |
| Evidencia | **gate-done** 2026-09-17 |
| Diferidos abiertos | D-TUR-17 usable · T6 padre |
| Próximo paso | N/A este slug — D-TUR-17 o T6 padre |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |

## No es

- Excel de Consulta Agenda (`turnos-agenda-consultas-export`).
- PDF cola (hijo print **gate-done**).
- Equipo north usable (D-TUR-17).
