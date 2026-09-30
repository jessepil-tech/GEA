---
title: SDD — T5.5 hijo · Excel consulta agenda
description: >-
  POI Exportar Excel (no sidecar de reportes) en Consulta Agenda Turnero.
  Padre T5.5 gate-done; south Exportar Excel live.
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export
---

# turnos-agenda-consultas-export

Padre: [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) T5.5 **gate-done**.  
PDF: [`turnos-agenda-consultas-pdf/`](../turnos-agenda-consultas-pdf/) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Clarify **FIRME Camino 1** 2026-09-16 (Francisco: arrancar Exportar Excel). D-TUR-63.  
G6 **PASS** 2026-09-16 (Francisco: «ok el excel esta saliendo bien»).  
Instalación de referencia: Call Center Demo (misma T5.5). Sin ramas `esClienteX()` en `generarReporteExcel`.  
**No WAIVE:** HIS `BBConsultaAgenda.generarReporteExcel` + `XLSParser` (HSSF `.xls`).  
**No** sidecar Reports. **No** embeber motor de reportes en Api.

Legacy: `consulta.xhtml` L214–216 · `generarReporteExcel`.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (Java `XLSParser`; JDBC consulta ya T5.5; no porta TURNOS BODY) | liberado |
| Rango Flyway | **ninguno** (solo lectura `ts.turno`) | n/a |
| Tablas `ts` que escribe | ninguna | — |
| Rama | `dev/dev` | — |

No pisa el BODY TURNOS ni V57–V58. Paralelo a P3 anunciador: sí (otro stream).

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.turno` rango hoy (grilla = Excel) | no | dump / T4–T5 | disponible |
| Combos centro/servicio/personal | no | T5 | disponible |
| `ts.paciente.nro_hc_anterior` | no | V31 | disponible (puede ir vacío) |
| `ts.te_persona` teléfono | no | V56 vacía | disponible (columna Excel vacía si 0 filas) |
| `mail_persona` | no | **no** en Flyway | columna Excel **presente**, valor NULL — no hijo |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrado) |
| Reservas | liberadas |
| Universo firmado | [spec.md](spec.md) · inventarios |
| Fixture | resuelto |
| Evidencia | 8 filas **verificado** ([verify-report.md](verify-report.md)) |
| Diferidos abiertos | T6 padre · D-TUR-17 · Excel de **otras** pantallas HIS |
| Próximo paso | ninguno en este corte |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy south + cabecera xls |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Disparador Excel |
| [inventario-validaciones.md](inventario-validaciones.md) | Vacío silencioso + fechas |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin ALTER |
| [inventario-geometria.md](inventario-geometria.md) | South 120px |

## No es

- Re-portar diseño ConsultaAgenda / emitter Excel del sidecar.
- Excel de T4 Consulta Agendas Generadas ni de otras pantallas HIS (`generarReporteExcel` × N).
- Equipo usable (D-TUR-17): header equipo omitido.
- T6 / menú `consultaTurnos*.xhtml`.
