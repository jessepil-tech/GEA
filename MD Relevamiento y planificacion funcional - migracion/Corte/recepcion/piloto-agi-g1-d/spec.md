---
title: SDD — Piloto AGI G1-d ticket PDF
description: Sidecar hospital-reports + PDF ticket recepción (camino BIRT).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-d
---

# Spec — Piloto AGI G1-d

Padre: [G1-c](../piloto-agi-g1-c/). Paridad de **emisión de ticket** con
`BBRecepcionarPaciente` / `ReportManager.printReport` sobre
`NroColaEsperaRecep.rptdesign`, **sin** impresora física.

## Resultado

1. Proceso JVM aparte **`Hospital-Reports`** (:8082) con `POST /api/v1/reports/run`
   → PDF (`reportId=NroColaEsperaRecep` + parámetros del ticket G1).
2. **Hospital-Api** no declara `org.eclipse.birt.*`. Cliente HTTP interno +
   `GET /api/v1/agi/recepciones/{id}/ticket.pdf`.
3. Confirmación G1 responde `ticketPdfUrl` relativo.
4. UI `/agi/recepcion`: enlace/descarga del PDF tras confirmar.
5. Impresora física / otros `.rptdesign` / Oracle como datasource del stack nuevo:
   **fuera**.

## Motor del slice (honestidad)

| Hito | Motor | Criterio |
|------|-------|----------|
| **G1-d gate** | Renderer de ticket por **parámetros** (OpenPDF) detrás del mismo contrato HTTP | Camino Api→sidecar→PDF→UI medible |
| **G1-d.birt** (follow-up / TSK) | Eclipse BIRT **4.24** + diseño adaptado a PG | Swap de engine sin cambiar contrato ni Api |

Motivo: el runtime BIRT no está en el monorepo; el valor del piloto es validar
aislamiento de proceso y el contrato (R3+R6 de
[`birt-runtime-destino.md`](../../../arquitectura/birt-runtime-destino.md)). El `.rptdesign`
legacy se conserva como referencia de parámetros; ejecución BIRT real = TSK
explícito, no bloquea el gate del camino.

## Parámetros (contrato `NroColaEsperaRecep`)

Mapeo mínimo desde ticket G1 (paridad con legacy `parameters.put`):

| Param reporte | Origen piloto |
|---------------|---------------|
| `titulo` | fijo / terminal (seed: “Autogestión”) |
| `leyenda` | opcional vacío |
| `centroAtencion` | seed / “Centro piloto” |
| `nroPreRecep` | `nroEspera` |
| `recepcion` / `lugarEspera` | `lugarEspera` |
| `servicio` | `servicio` |
| `instrucciones` | `instruccion` |
| `fechaImpresion` | `creadoEn` |
| `paciente` | `pacienteNombre` |
| `mensajes` | `mensajeValidador` |
| `sectorAmb` / `ambienteAmb` | vacío o seed |
| `tipoDocPac` / `nroDocPac` | si disponibles en lookup |

SQL del `.rptdesign` (`sysdate`, barcode triage) **no** es requisito del gate G1-d.

## CA

1. Tras `POST /recepciones` → 201 con `ticketPdfUrl`.
2. `GET …/ticket.pdf` (JWT) → `200` + `Content-Type: application/pdf` + magic `%PDF`.
3. Sidecar caído → Api responde error controlado (502/503), no stacktrace BIRT en Api.
4. Smoke: confirmar + descargar PDF; IT con motor stub o sidecar.
5. UI: botón/enlace “Descargar ticket PDF”.
6. WAIVE: impresora física; paridad visual pixel-perfect vs legacy BIRT; BIRT 4.24 en runtime.

## Fuera de alcance

- Triage / `TicketOrdServAmb` / otros 407 diseños.
- Embeber BIRT en `presentation-api`.
- Datasource Oracle del stack nuevo.
- Viewer BIRT / Tomcat.
