# R5 — HistoriaClinica + EpicrisisGYE

Slice del frente reportes: migrar al sidecar **Hospital-Reports** dos diseños BIRT vigentes.

| reportId | Origen legacy | Estado |
|----------|---------------|--------|
| `HistoriaClinica` | `HOSPITAL_2/.../HistoriaClinica.rptdesign` (~329 KB, 9 datasets) | Dialect convertido; **gaps packages/TMP cerrados** ([`gaps-hc-packages.md`](gaps-hc-packages.md)) |
| `EpicrisisGYE` | `HOSPITAL_2/.../EpicrisisGYE.rptdesign` (~828 KB, 25 datasets) | Dialect convertido; packages form/matricula pendientes ([`gaps-epicurisis-gye.md`](gaps-epicurisis-gye.md)) |

**Fuera de alcance:** `HistoriaClinica(old)`, `EpicrisisGYEMATERDEI` / `Epicrisis`, `HistoriaClinicaCARATULA` / `EVENTO`, wiring Hospital-API (R6).

Guía registro: [`register-report-sidecar.md`](../../../arquitectura/register-report-sidecar.md) · Plan R5: [`../../birt-runtime-destino.md`](../../../arquitectura/birt-runtime-destino.md).

## Params (fuente de verdad = diseño)

### HistoriaClinica

| Param | dataType | Notas |
|-------|----------|--------|
| `idPaciente` | float | |
| `usuario` | string | TMP / generateEventosHC |
| `where_amb` / `where_int` / `where_cir` | string | filtros dinámicos |
| `tipo_impresion` | string | |
| `idCentroAteAntecedenteServ` / `idServicioAntecedenteServ` | float | |
| `DB_*` | string | inyecta sidecar |

**Gap callers:** `BBInternacion.reporteInternacion` (y clones guardia) pasan un mapa **stale** (`idCentroAte`, `conceptoIngreso*`, …) que no coincide con el diseño. La impresión HC “completa” usa CARATULA/EVENTO (otro alcance). Fixtures del sidecar = params del diseño.

### EpicrisisGYE

| Param | dataType | Notas |
|-------|----------|--------|
| `idInternacion` | float | |
| `borrador` | float | 0=confirm, 1=draft |
| `titulo` | string | p.ej. `"Epicrisis"` |
| `cierreEpicrisis` | string | opcional |
| `urlLogo` / `nombreCliente` | string | ReportManager / caller |
| `DB_*` | string | inyecta sidecar |

## Datasets

**HistoriaClinica (9):** PACIENTE, PROBLEMAS_ACTIVOS, HISTORIA_CLINICA, TMP_RPT_HISTORIA_CLINICA, PROBLEMAS_INACTIVOS, MEDICACION_CRONICA, BALANCE_HIDRICO (SP), ANTECEDENTES GENERALES, ANTECEDENTES_SERVICIO.

**EpicrisisGYE (25):** INTERNACION, COD_CIE_MOTIVO_INT, ANTECEDENTES PAC, ANAMNESIS, EXAMEN_FISICO, EVALUACION_ENF, ANTECEDENTES SERV PAC, PROBLEMAS_CRONICOS, ALERGIAS, MEDICACION_CRONICA, DIAGNOSTICOS, EVOLUCIONES, INTERCONSULTAS, NACIMIENTOS, DATOS_PROCEDIMIENTO, SIGNOS_VITALES, ANTROPOMETRIA, MEDICAMENTOS_ADMINISTRADOS, PRESTACIONES_REALIZADAS, SCORES, DET_AISLAMIENTO, FIRMA_PERSONAL, MARICULA_ENFERMERO, MARICULA_MEDICO, TRIAGE_AMB.

## Effort / blockers

| Reporte | Effort | Cuello |
|---------|--------|--------|
| HistoriaClinica | HIGH | Packages `ts.historia_clinica.*`, SP balance hídrico, TMP + `generateEventosHC` |
| EpicrisisGYE | MEDIUM | Sobre todo `ROWNUM` (17); NVL/DECODE/SYSDATE mecánicos |

Ver [`gaps-hc-packages.md`](gaps-hc-packages.md) (F2).

## Criterios de hecho

- [x] Diseños en `Hospital-Reports/designs/` + registrados en `hospital.reports.registered`
- [x] ITs opt-in `HistoriaClinicaBirtIT` / `EpicrisisGYEBirtIT` (skip limpio sin `BIRT_IT`)
- [x] EpicrisisGYE: dialect Oracle→PG en queryText + driver PG + blockers packages/DDL documentados ([`gaps-epicurisis-gye.md`](gaps-epicurisis-gye.md)) — Docker BIRT intenta render; FAIL `ts.internacion` inexistente en PoC
- [x] HistoriaClinica: lista cerrada de gaps packages/TMP/DDL ([`gaps-hc-packages.md`](gaps-hc-packages.md)) + dialecto + driver PG (sin fingir paridad PDF)
- [x] Bitácora R5 actualizada en `birt-runtime-destino.md` / `sidecar-reports-avance.md`

## Código

- Sidecar: repo **Hospital-Reports**
- Docs programa: este slice (regla `repos.md`)
