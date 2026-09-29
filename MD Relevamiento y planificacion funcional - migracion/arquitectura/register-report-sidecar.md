# Registrar un reporte BIRT nuevo en Hospital-Reports

Guía del programa (frente R5): agregar un reporte al sidecar **sin** tocar código del engine ni del worker.
Código y diseños viven en **Hospital-Reports**.

## Quick path

1. Agregar el diseño en classpath: `Hospital-Reports/src/main/resources/designs/<reportId>.rptdesign`.
2. Listar `<reportId>` en `hospital.reports.registered` (CSV en `application.properties`).
3. Agregar una subclase opt-in de `AbstractBirtRenderIT` en `src/test/.../birtit/`.

| Último paso | Resultado esperado |
|---|---|
| `mvn test` | IT OpenPDF pasan; el IT BIRT nuevo se skippea sin `BIRT_IT` |
| `BIRT_IT=true` + runtime + DB (`tools/run-birt-it.sh`) | El IT BIRT renderiza PDF válido |

## Details

| Área | Decisión |
|---|---|
| Diseño | Convención `designs/<reportId>.rptdesign`. Override: `hospital.reports.birt.design.<reportId>` |
| Registro | `hospital.reports.registered=NroColaEsperaRecep,…` → 1 `GenericBirtReportEngine` por id |
| Tipado de params | Por **`dataType` del `.rptdesign`** (`ScalarParameterHandle.getType()`). Fuente de verdad = diseño |
| Fallback OpenPDF | Solo `NroColaEsperaRecep` hoy (`NroColaEsperaRecepParamEngine`) |
| Worker | Tras tocar `birt-worker/`: `tools/build-birt-worker.sh` |

### Ejemplo: `ConsultaAgendaGeneradas` (turnos)

1. Diseño en Hospital-Reports: `designs/ConsultaAgendaGeneradas.rptdesign` (ya en `hospital.reports.registered`).
2. Hospital-Api: `GetConsultaAgendaGeneradasPdfQueryHandler` + `GET /api/v1/turnos/grilla/consulta-agendas/imprimir.pdf` — ver [`integracion-hospital-reports.md`](../../../Hospital-API/docs/sdd/integracion-hospital-reports.md) en repo Hospital-Api.
3. Hospital-Web: `downloadConsultaAgendaGeneradasPdf` en grilla consulta.
4. SDD verify: [`turnos-consulta-agendas-imprimir/verify-report.md`](../cortes/turnos/turnos-consulta-agendas-imprimir/verify-report.md).

### Ejemplo: `TicketOrdServAmb`

1. Copiar/adaptar `.rptdesign` → `designs/TicketOrdServAmb.rptdesign` (SQL Oracle→PG).
2. `hospital.reports.registered=NroColaEsperaRecep,TicketOrdServAmb`.
3. Subclase de `AbstractBirtRenderIT` con `reportId()`, `designName()`, `defaultParams()`.

## Checklist

- [ ] Elegido el `.rptdesign` legacy correcto (callers Java; no fusionar variantes)
- [ ] `.rptdesign` en classpath con nombre = `reportId` (o override)
- [ ] `reportId` en `hospital.reports.registered`
- [ ] Column bindings / `row["…"]` en **minúsculas** (PG) — [`regla-birt-columnas-minusculas.md`](../canon/regla-birt-columnas-minusculas.md); tool: `Hospital-Reports/tools/lowercase-birt-column-bindings.py`
- [ ] Un solo `examples/api-requests/<reportId>.json`
- [ ] Callables reales en `sql/packages-pg/` (sin mocks) + seed si aplica
      (tablas `ts.<tabla>`; callable `personas.*` / `historia_clinica.*`)
- [ ] IT opt-in extiende `AbstractBirtRenderIT` (gate `BIRT_IT`)
- [ ] Smoke PDF vs legacy; pitfalls nuevos → bitácora del playbook
- [ ] `mvn test` default verde; IT BIRT skippean limpio

## Proceso / pitfalls

Migración **incremental** (un reporte por corte), restricciones y bitácora viva:

- [`regla-migracion-reportes-birt.md`](../canon/regla-migracion-reportes-birt.md)
- [`playbook-migracion-reporte-birt.md`](playbook-migracion-reporte-birt.md)

Rules del agente en `Hospital-Reports/.cursor/rules/` (`migrate-report-incremental`,
`packages-pg-ts-qualify`, `birt-design-pg-pitfalls`, `sql-reports-callables-seeds`,
`api-requests-one-per-report`).

## Next step

Plan R5 y estado del motor: [`../birt-runtime-destino.md`](birt-runtime-destino.md) §4 ·
[`sidecar-reports-avance.md`](../estado/sidecar-reports-avance.md).
