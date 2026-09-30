---
title: SDD — T4 hijo · imprimir consulta agendas generadas (BIRT)
description: >-
  PDF Consulta Agendas Generadas vía Hospital-Api → Hospital-Reports
  (reportId ConsultaAgendaGeneradas). Paridad BBConsultaAgendasGeneradas.actBtnImprimirTurnos.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.turnos-consulta-agendas-imprimir
---

# T4 hijo — Imprimir consulta agendas (`turnos-consulta-agendas-imprimir`)

**Padre:** [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) (pantalla CONS).  
**Hermano:** [`turnos-consulta-agendas-drilldown/`](../turnos-consulta-agendas-drilldown/) (calendario + turnos del día).  
**Diseño BIRT:** `ConsultaAgendaGeneradas` (repo Hospital-Reports; migración PG documentada allí).

## Alcance

| In | Out |
|----|-----|
| Botón **Imprimir** en consulta agendas (modo servicio / profesional) | Filtro **equipo** en impresión (D-TUR-17) |
| `GET .../consulta-agendas/imprimir.pdf` en Hospital-Api | Motor BIRT embebido en Api |
| Descarga blob en Hospital-Web | `estadoTurno` distinto de “todos” al imprimir (legacy relevar; hoy vacío) |
| Delegación `ReportsPort` → sidecar `:8082` | Paridad columna teléfono paciente sin `personas.f_get_persona_telefono` |

## Flujo

```
Web (grilla-turnos-consulta) → Api TurnosGrillaResource
  → GetConsultaAgendaGeneradasPdfQueryHandler
  → ConfigurableReportsAdapter (mode=http en %dev)
  → POST Hospital-Reports /api/v1/reports/run { reportId: ConsultaAgendaGeneradas, parameters }
  → PDF inline
```

## API (Hospital-Api)

```
GET /api/v1/turnos/grilla/consulta-agendas/imprimir.pdf
  ?idCentroAte&idServicio&fecha
  [&idPersonal][&centroAtencion][&servicio][&personal]
```

- `idPersonal` omitido → `"0"` (todos; modo servicio).
- `usuario` en pie del PDF = login JWT (`requireCallCenterGate`).
- Config dev: `hospital.reports.mode=http`, `hospital.reports.base-url=http://localhost:8082`.

## Web (Hospital-Web)

- `grilla-turnos-consulta.component.ts` → `TurnosGrillaUseCase.downloadConsultaAgendaGeneradasPdf`
- `TurnosRepositoryImpl`: `GET .../imprimir.pdf` → `Blob` + descarga local.
- Habilitación: día seleccionado con turnos en panel sur (misma sesión de consulta).

## Verify

Ver [`verify-report.md`](verify-report.md).

| Capa | Doc | Comando |
|------|-----|---------|
| Web e2e (mocks) | [`migrated-flow.md`](migrated-flow.md) | `npm run e2e -- e2e/grilla-turnos-consulta.spec.ts` |
| Legacy discovery | [`legacy-flow.md`](legacy-flow.md) | variables `LEGACY_E2E_*` + spec en `e2e/legacy/` |
| Stack real BIRT | verify-report § G6 | **gate-done** 2026-09-04 |

## Gaps / diferidos (sin silencio)

| Ítem | Estado | Nota |
|------|--------|------|
| Columna **equipo** en dataset | diferido(D-TUR-17) | `equipo_serv_centro` no migrada; SQL devuelve null |
| **Teléfono** paciente | diferido(reports-packages-pg) | `personas.f_get_persona_telefono` no en piloto |
| Smoke manual stack local | **done G6** | Padre T4 smoke ops 2026-09-04 |
