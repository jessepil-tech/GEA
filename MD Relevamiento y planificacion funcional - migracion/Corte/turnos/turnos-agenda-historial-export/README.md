---
title: SDD — T6.3 hijo · Excel Historial Turnos
description: >-
  South Exportar Excel de historialTurno.xhtml: POI HSSF desde JDBC hist.
  14 columnas; sin hora suelta; Fecha/FechaModifica = dd/MM/yyyy HH:mm.
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial-export
---

# turnos-agenda-historial-export

Padre: [`turnos-agenda-historial/`](../turnos-agenda-historial/) T6.3 **gate-done**.  
**Gate:** **PASS / gate-done** 2026-09-18.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Clarify **FIRME Camino 1** D-TUR-73. Francisco: «si habramoslo ahora».  
Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()` en `actBtnExportarExcel`.  
**No** sidecar. **No** SheetJS. **No** es el Excel de Consulta Agenda ni de la cola.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (Java `XLSParser`; JDBC hist ya T6.3) | n/a · no toma lock TURNOS |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | ninguna | n/a |
| Rama | `dev/t63-historial-turnos` | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Filas `ts.hist_turno` | no | T5 otorga/libera | dump COUNT=17 · G6 ve grilla |
| Combos north | no | T5 | disponible |
| Equipo usable | no | D-TUR-17 | N/A en xls (no hay col Equipo suelta) |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** publicado · **gate-done** |
| Reservas | BODY n/a · Flyway n/a · sin escritura (liberar n/a) |
| Universo firmado | [spec.md](spec.md) — **FIRME** Camino 1 · semilla south `actBtnExportarExcel` (1 bean; techo OK) |
| Fixture | misma hist que Consultar |
| Evidencia | [verify-report.md](verify-report.md) · G6 «ok se ve bien el excel» |
| Diferidos abiertos | D-TUR-17 · T6 padre · NFR padre T6.3 |
| Próximo paso | ninguno de este hijo; padre T6.3 sigue abierto |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |

## No es

- Excel de Consulta Agenda (`turnos-agenda-consultas-export`).
- Excel de cola Reasignación (`turnos-agenda-cola-reasignar-export`).
- Popup info / lista (padre T6.3).
- Equipo north usable (D-TUR-17).
- Menú `consultaHistorialTurno`.
