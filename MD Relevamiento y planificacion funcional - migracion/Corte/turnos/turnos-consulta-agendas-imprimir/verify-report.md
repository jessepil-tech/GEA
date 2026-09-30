---
title: Verify — turnos-consulta-agendas-imprimir
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.turnos-consulta-agendas-imprimir
---

# Verify — Imprimir consulta agendas (BIRT)

**Gate:** **PASS / gate-done** 2026-09-04 · implementación Api+Web + smoke G6 padre.

## Capacidades

| Capacidad | Estado | Evidencia |
|-----------|--------|-----------|
| Endpoint PDF Api | **done** | `TurnosGrillaResource` `GET .../consulta-agendas/imprimir.pdf` |
| Handler CQRS + params BIRT | **done** | `GetConsultaAgendaGeneradasPdfQueryHandler` · `reportId=ConsultaAgendaGeneradas` |
| Cliente HTTP Reports | **done** | `ConfigurableReportsAdapter` · `%dev.hospital.reports.mode=http` |
| Botón + descarga Web | **done** | `grilla-turnos-consulta` · `downloadConsultaAgendaGeneradasPdf` |
| IT Api (stub Reports) | **done** | `TurnosGrillaResourceIT.consultaAgendas_imprimirPdf_stub_ok` |
| Render BIRT real E2E | **done** (dev) | Reports `:8082` + PG piloto; filas en PDF (ej. turno LIBRE 08:00 2026-09-14) |
| Playwright e2e Web (mocks Api) | **done** | `Hospital-Web/e2e/grilla-turnos-consulta.spec.ts` |
| Smoke manual stack real (Api+Reports+PG) | **done G6** | Padre T4 smoke ops 2026-09-04 · fixture piloto 2026-09-14 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | [`migrated-flow.md`](migrated-flow.md) |
| Fixture | mocks CI · PG piloto `HOSPITAL-DEMO` / `CLINICA MEDICA` / `2026-09-14` (G6) |
| Legacy e2e | **no** — sin fixture Oracle vigente para `origin`; no scan |

## Paridad legacy

| Aspecto | Estado | Nota |
|---------|--------|------|
| Botón Imprimir habilitado con contexto día | **done** | Paridad mínima con `actBtnImprimirTurnos` |
| Encabezado centro/servicio/fecha/personal | **done** | Params `centroAtencion`, `servicio`, `personal`, `fechaDesde/Hasta` |
| Grilla turnos del día en PDF | **done** (piloto) | Dataset `AGENDAS`; validado HOSPITAL-DEMO / CLÍNICA MÉDICA / 2026-09-14 |
| Columna equipo | diferido(D-TUR-17) | Subquery legacy sin tabla PG |
| Teléfono paciente | diferido(reports-packages-pg) | Función no instalada en piloto |
| Filtro `estadoTurno` al imprimir | pendiente relevar | Api envía `""` (todos); confirmar vs legacy |

## Verify Playwright (Web — CI)

```bash
cd Hospital-Web
npm run e2e -- e2e/grilla-turnos-consulta.spec.ts
```

Cubre: filtros → matriz → mes `S` → día → turnos → **Imprimir** → descarga PDF (mock).  
Flujo documentado: [`migrated-flow.md`](migrated-flow.md).

## Verify Playwright (legacy — opt-in)

Requiere `LEGACY_E2E_BASE_URL`, `LEGACY_E2E_USER`, `LEGACY_E2E_PASSWORD`.  
Ver [`legacy-flow.md`](legacy-flow.md).

## Smoke integración (checklist G6)

1. Hospital-Reports en `:8082` (Docker dev, misma PG que Api). — **PASS** 2026-09-04
2. Hospital-Api perfil **dev** (`hospital.reports.mode=http`). — **PASS**
3. Web → Consulta agendas → mes `S` → día con turnos → **Imprimir**. — **PASS**
4. PDF con filas (hora, duración, centro, servicio); pie con usuario y fecha impresión. — **PASS**

## Resultado

**PASS / gate-done** (hijo T4).  
**Implementación:** done Api + Web + integración Reports.  
**Verify automatizado UI:** done Playwright (mocks).  
**Gaps sin WAIVE:** `estadoTurno` al imprimir (pendiente relevar); D-TUR-17 equipo; teléfono paciente (`reports-packages-pg`).
