---
title: Plan — T5.1 Turnos agenda ficha paciente
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-ficha-paciente
---

# Plan — T5.1

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Reusar V31 paciente/convenio/plan/persona; Flyway **solo** si G0 detecta columnas faltantes para ficha u otros centros |
| Core | DTO ficha paciente + criterios búsqueda (sin lógica turnos mutación) |
| Application | CQRS: `BuscarPacienteAgenda*`, `GetFichaPacienteAgenda*`, `BuscarConvenioAgenda*`, `ListPlanesConvenio*`, `ListTurnosOtrosCentros*` |
| Infrastructure | JDBC lectura; portar query `selectTurnosOtrosCentros` a Java (golden escenarios) |
| Presentation | `/api/v1/turnos/agenda/pacientes/...` · `convenios/...` · `otros-centros` |
| Web | Extender `turnos-agenda.component.ts` + dialogs `PacienteBuscador` / `ConvenioBuscador` (patrón hab-buscadores) |
| Tests | IT búsqueda/ficha/otros-centros; e2e ampliado T5 |

Orden: Clarify **FIRME** ✅ → inventarios G0 ✅ → DDL G1 (si aplica) → API lectura G2 → Gate UI G3 → Web G4 → e2e G5 → smoke + verify G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME Camino 1 + inventarios copy/validaciones/interacción/DDL — **done 2026-09-07** |
| G1 | Flyway extra — **N/A** V31/V47 |
| G2 | API búsqueda + ficha + otros centros + IT — **done 2026-09-07** |
| G3 | **Gate UI arranque** — **done 2026-09-08** |
| G4 | Web north + west cableados a API — **done 2026-09-08** |
| G5 | e2e Playwright ampliado — **done 2026-09-08** |
| G6 | Smoke stack real + verify PASS — **done 2026-09-08** |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| `buscadorPaciente.xhtml` muy grande | v1: columnas mínimas agenda (apellido, nombre, doc, conv); filtros avanzados diferidos |
| `selectTurnosOtrosCentros` complejo | Golden 1–2 escenarios; defer acción consultar agenda |
| Duplicar ABM paciente | Explícito no objetivo; solo lectura + búsqueda |
| Romper e2e T5 | Fixture mantiene ids; nuevo test escenario buscador |
| Mezclar elegibilidad WS | Rechazar en PR; stub documentado en verify |

## Dependencias

- T5 gate-done (`TurnosAgendaPort` mutaciones + grilla)
- T1 call center JWT
- V31 maestros paciente/convenio/plan
- Patrón buscadores: [`turnos-hab-buscadores/`](../turnos-hab-buscadores/)

## Fuera del plan v1

Accordion 170px; datosPaciente tabs; nuevo paciente; WS elegibilidad; drag-drop; consultar agenda otros centros; T6/T7.
