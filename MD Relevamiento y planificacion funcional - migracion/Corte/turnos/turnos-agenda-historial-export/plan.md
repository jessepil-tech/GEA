---
title: Plan — T6.3 hijo · Excel Historial Turnos
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial-export.plan
---

# Plan — Excel Historial Turnos

Clarify **FIRME** Camino 1 D-TUR-73. Gate UI **N/A** xhtml nuevo: habilitar south 150px ya medido en padre.

## G0 — SDD

- [x] README reservas n/a · spec · plan · tasks · verify.

## G2 — API

- [x] `HistTurnoExcelSheet` título `Historial Turnos`, headers vacío, 14 cols.
- [x] `GetHistTurnoExcelQuery` + Handler: `validateHistTurno` + `listarHistTurno` + `ExcelExportPort.renderXls`.
- [x] `GET .../agenda/historial/exportar.xls` **sin** `requireCallCenterGate`.
- [x] Tests: 14 headers; 0 filas = bytes no vacíos; `horaDesde > horaHasta` = `WRONG_INTERVAL_HOUR`; **no** `DATE_3`.

## G4 — Web

- [x] Repo/use-case `downloadHistExcel`.
- [x] South `Exportar Excel` enabled; blob `historial-turnos-${fechaDesde}.xls`.
- [x] Fixture e2e stub `exportar.xls`.

## G5 — verify

- [x] Handler tests (3 passed).
- [x] e2e Historial 6 passed.
- [x] `./tools/verificar-sdd.sh turnos-agenda-historial-export`.

## G6

- [x] Francisco abre el xls — «ok se ve bien el excel» 2026-09-18.
