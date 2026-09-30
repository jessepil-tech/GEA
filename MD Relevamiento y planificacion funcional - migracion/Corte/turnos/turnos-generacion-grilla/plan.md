---
title: Plan — T4 Turnos generación de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-generacion-grilla
---

# Plan — T4

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Flyway V43+: tablas aux (`turno_a_reasignar`, `hist_turno`, `eliminacion_agenda_turnos`, `fecha_feriado` si falta); FK `turno.id_grp_prest_tur_*` |
| Core | `TurnosGeneracionPort` + dominio observación + reglas solape/hab/feriado |
| Application | CQRS `GenerarGrilla*`, `EliminarGrilla*`, `ConsultaAgendasGeneradas*` |
| Infrastructure | JDBC lectura T2/T3 + escritura `ts.turno`; **sin** Oracle SP en runtime |
| Presentation | `/api/v1/turnos/grilla/...` (generar, eliminar, consulta) |
| Web | 3 rutas bajo TURNOS / agenda; buscadores hab reutilizados |
| Tests | Golden / IT por modo serv+pers; fixtures V42 seed |

Orden: Clarify **FIRME** (2026-09-01) → inventarios G0 → Flyway G1 → golden + port → API → UI → smoke → verify + matriz.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME + inventario validaciones/copy (borrador) |
| G1 | Flyway aux + FK grp; seed feriado demo opcional |
| G2 | Port generación serv+pers + tests golden + API generar + IT |
| G3 | Port eliminar + `turno_a_reasignar` + API + IT |
| G4 | Query consulta agendas + API |
| G5 | UI 3 pantallas + paridad UI |
| G6 | Smoke + verify-report PASS + backlog/matriz |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Complejidad SP ~2k LOC | Golden tests por escenario; no big-bang UI |
| Fork seed AGI OTORGADO | Rangos smoke dedicados; doc convivencia DEV |
| Horario especial omitido en silencio | D-TUR-20 diferido explícito en verify |
| Scope T6 (suspender) mezclado | Rechazar en PR; menú legacy separado |
| FKs turno | Solo grp en T4; resto pendientes-solo-oracle |
| `fecha_feriado` ausente | G1 Flyway + seed mínimo |

## Dependencias

- T3 gate-done (horarios pers+serv)
- T2 gate-done (hab)
- T1 gate parcial (call center JWT)
- `ts.turno` V31
- Buscadores hab (gate-done)

## Fuera del plan T4 v1

Equipo; horario especial en algoritmo; SMS/mail; suspender/reemplazo; T5 agenda;
entry UI desde T3 horario; levantamiento FKs completas turno.
