---
title: Tasks — turnos-consulta-agendas-drilldown
status: done
last_updated: 2026-09-04
---

# Tasks — Drill-down consulta agendas

## API

- [x] Core records `ConsultaAgendaDiasRequest`, `ConsultaAgendaTurnosDiaRequest`, `DiaAgendaConsulta`, `TurnoAgendaConsulta`
- [x] Extend `TurnosGeneracionPort` + `TurnosGeneracionValidation` (anio/mes/grupo exacto)
- [x] JDBC `listarDiasAgenda` / `listarTurnosAgendaDia` (feriados + agregación estado, joins persona/centro/convenio)
- [x] CQRS handlers `consultaagendadias` + `consultaagendaturnosdia` registrados en `CqrsConfiguration`
- [x] Endpoints `GET .../consulta-agendas/dias` y `GET .../consulta-agendas/turnos` (después de consulta-agendas)

## Web

- [x] Models + repo + use-case drill-down
- [x] Consulta: celdas `S` clickeables; panel sur calendario + tabla turnos + leyendas
- [x] Clear drill-down en nueva Consulta
- [x] Tokens UI / clases CSS día y estilo fila en `grilla-turnos-labels.ts`

## Imprimir (hijo)

Ver [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/) — **done** Api+Web 2026-09-04.

## Verify (manual)

- [ ] Click `S` carga calendario del mes/grupo
- [ ] Click día carga turnos
- [ ] Leyendas visibles