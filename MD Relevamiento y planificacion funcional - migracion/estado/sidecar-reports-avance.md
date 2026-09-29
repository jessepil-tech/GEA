# Avance — Sidecar Hospital-Reports

Snapshot del frente reportes (transferencia / retoma). Fecha snapshot base: **2026-08-26**; actualización piloto turnos: **2026-09-04**.
Código: repo **Hospital-Reports** rama `dev/dev` (R3.2/R3.3/Docker/Terraform **commiteados**).

## Snapshot

| Ítem | Estado |
|------|--------|
| R3 esqueleto + `NroColaEsperaRecep` | Hecho (G1-d.birt) |
| R3.1 logo/barcode | Ingeniería done; UAT impresora on-site |
| R3.2 harness BIRT real | Hecho (opt-in `BIRT_IT=true`) |
| R3.3 `generic-report-engine` | **Hecho, commiteado y SDD archivado** |
| R5 oleada HC + EpicrisisGYE | **Scaffold + dialect done**; gaps packages documentados — [`r5-historia-clinica-epicurisis-gye/`](../cortes/plataforma/r5-historia-clinica-epicurisis-gye/) |
| PoC AWS ECS Fargate | **Desplegada + E2E OK** (PDF BIRT real) |
| `ConsultaAgendaGeneradas` E2E piloto turnos | **gate-done** Api+Web+Reports G6 2026-09-04 — [`turnos-consulta-agendas-imprimir`](../cortes/turnos/turnos-consulta-agendas-imprimir/) |
| Terraform prod (`exposure_mode=private`) | Diseñada; no aplicada |

## Arquitectura (recordatorio)

- Quarkus `:8082` → `POST /api/v1/reports/run`
- BIRT 4.24 en **JVM aparte** (`ProcessBuilder` + `birt-worker.jar`)
- Fallback OpenPDF si `engine=openpdf` o sin runtime
- Registro multi-reporte: `hospital.reports.registered` + `designs/<reportId>.rptdesign`

## R3.3 — decisiones aceptadas

| Decisión | Detalle |
|----------|---------|
| Diseño | Convención `designs/<reportId>.rptdesign` |
| CDI | `ReportEngineProducer` → `@Produces List<ReportEngine>`; Registry usa `List` (ArC no expande en `Instance`) |
| Params | Tipado por `dataType` del diseño (no por nombre) |
| Infra vs negocio | DB/fonts/logo = infra; no forzar `nroTriageAmb=-1` |

## PoC AWS (resumen)

Ver detalle: [`../despliegue-hospital-reports-aws.md`](../arquitectura/despliegue-hospital-reports-aws.md) §4.

- Cluster `osw-gea-reports` · ECR `grupogea/reports:0.2.0` · RDS `grupogea-hospital`
- Health UP · PDF ~2329 bytes · Creator BIRT 4.24
- IP task efímera; profile AWS `sdilenardo`

## Pendientes

1. ~~Commit del working tree en Hospital-Reports~~ **hecho 2026-08-26**
2. `terraform validate` de `terraform/` completa
3. **R5** [`r5-historia-clinica-epicurisis-gye/`](../cortes/plataforma/r5-historia-clinica-epicurisis-gye/) — diseños + dialect + driver PG; Docker BIRT FAIL por `ts` vacío en PoC (ver gaps)
4. ~~Archivar change SDD `generic-report-engine`~~ **hecho (Engram)**
5. Prod: Terraform privada + Secrets Manager
6. UAT impresora térmica (on-site)
7. Ports R4 faltantes: `p_get_valor_det_form_col`, matricula/especialidad, TMP HC / balance hídrico
8. Alinear RDS PoC schema `ts` (hoy tablas en `public`) + seed para PDF E2E clínicos

## Docs canónicos (programa)

| Doc | Rol |
|-----|-----|
| [`../birt-runtime-destino.md`](../arquitectura/birt-runtime-destino.md) | Plan R0–R7 |
| [`../despliegue-hospital-reports-aws.md`](../arquitectura/despliegue-hospital-reports-aws.md) | AWS / PoC |
| [`register-report-sidecar.md`](../arquitectura/register-report-sidecar.md) | Cómo registrar un reporte |
| [`r5-historia-clinica-epicurisis-gye/`](../cortes/plataforma/r5-historia-clinica-epicurisis-gye/) | Oleada R5 HC + EpicrisisGYE |
| [`estado-piloto-vs-general.md`](estado-piloto-vs-general.md) | Snapshot piloto |
| [`backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md) | Orden de trabajo |

## Next step

Ports R4 de helpers HC/form (`p_get_valor_det_form_col`, matricula) + datos clínicos para PDF E2E de EpicrisisGYE; TMP/`generateEventosHC` para HistoriaClinica.
