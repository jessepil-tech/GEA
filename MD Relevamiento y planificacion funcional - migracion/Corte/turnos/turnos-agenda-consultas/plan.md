---
title: Plan — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-consultas
---

# Plan — Consulta Agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Sin ALTER. Lectura `ts.turno` (+ joins persona/convenio ya T5). |
| API | **GET o POST** consulta rango (CQRS Query). **GET PDF** → `ReportsPort` `reportId=ConsultaAgenda` (patrón T4). No clonar `f_get_turnos_fecha`. No embeber BIRT. Filtros opcionales; estado `LIBRE`/`OTORGADO`/vacío=todos; `incluyeSobreturnos` (tabla, no PDF). |
| Web | Vista `consulta` en `turnos-agenda`: accordion live; north+tabla; Imprimir live (cable); Excel disabled. Sin calendario. PDF filas = hijo. |
| Tests | Ampliar `e2e/turnos-agenda.spec.ts` |

Orden: Clarify FIRME → G0 **hecho** → G1 N/A Flyway → G2 API → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + inventarios — **docs 2026-09-14** |
| G1 | Sin Flyway |
| G2 | Query JDBC + PDF handler sidecar + Resource delgado + IT |
| G3 | Gate UI `consulta.xhtml` **antes** de template |
| G4 | Web: Turnero + north + tabla + infoTurno + Imprimir |
| G5 | e2e abre / toast fechas / filas / Imprimir stub PDF |
| G6 | Smoke Francisco UI + grilla — **PASS** 2026-09-15. PDF valores = `turnos-agenda-consultas-pdf` |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Confundir con T4 consulta agendas | Spec D-TUR-59; otra ruta |
| Portar package `f_get_turnos_fecha` | JDBC filtros sobre `ts.turno` (paridad T5 grilla) |
| Excel en v1 | Botón disabled + hijo `turnos-agenda-consultas-export` |
| PDF columnas vacías (G6 visual) | Cable T5.5; hijo `turnos-agenda-consultas-pdf` |
| Re-portar `ConsultaAgenda.rptdesign` | **No.** Cable a sidecar existente |
| Habilitar equipo | D-TUR-17 |
| Dejar calendario 260 | HIS no lo tiene en esta hoja |
| Sobreturno live en consulta | HIS `disabled` si `idPMI ne Agenda` |

## Fuera del plan v1

Pre-agenda; T6; Excel POI; PDF con valores (`turnos-agenda-consultas-pdf`); lista espera; `consultaTurnos*.xhtml`. Re-migración BIRT.
