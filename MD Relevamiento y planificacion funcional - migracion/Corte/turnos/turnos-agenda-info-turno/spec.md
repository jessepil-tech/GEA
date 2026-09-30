---
title: Spec — T5.1e chrome infoTurno
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno
---

# Spec — Chrome Información Turno

## Problema

T5 cobró el acto (reserva → poll 59 s → Asignar / Cancelar libera). El dialog Web es un `dl` de 6 campos. HIS `infoTurno.xhtml` es un modal **1200×520** con paneles Datos paciente / Datos turno|sobreturno / observaciones / preparación / requisitos / documentación.

## Resultado

Misma ruta. Reescribir `turnos-info-turno-dialog` al xhtml (camino **un turno**, no repetidos/múltiples). Datos: ficha T5.1 + fila grilla/sobreturno + `listDocReq` T5.1c. Acto otorga/cancelar **sin cambio**.

## Clarify — **FIRME** Camino 1 (2026-09-10)

| # | Pregunta | Respuesta FIRME | Evidencia |
|---|---------|-----------------|-----------|
| 1 | ¿Pipeline? | T5 + T5.1 ficha + T5.1c doc-req + T5.1d cobros display. | padres gate-done |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. Un dialog para Asignar LIBRE, Información, y sobreturno. | `asignacionTurnos.xhtml` L274 |
| 3 | ¿Happy path UI? | Header `información_turno`. Closable **solo** lectura (HIS `closable=!otorgaTurno`). Paneles según xhtml un-turno. Footer 120px Asignar/Cancelar o Volver. | `infoTurno.xhtml` |
| 4 | ¿Ciclo de vida? | Igual T5: RESERVADO → OTORGADO / Cancelar libera. Observaciones y fecha prescripción **chrome**; persist al otorgar → **diferido** `turnos-agenda-info-turno-persist`. | T5 RF |
| 5 | ¿Errores? | Reusa T5 (lock, pagar, saldo). Toast sin `/500`. | T5 / T5.1d |
| 6 | ¿Fuera? | Tabla repetidos/múltiples **diferido**. Preparación/requisitos **filas** sin API → chrome vacío + emptyMessage; API → `turnos-agenda-info-turno-prest`. Imprimir preparación (solo recepción) **N/A** agenda call center / T7. tipoPaciente sin columna ficha → input vacío (no inventar). Equipo D-TUR-17. | xhtml L164–274 · L150–159 |
| 7 | ¿Gate UI? | Inventarios G0. Dialog **1200×520 = cuerpo** (titlebar aparte). `InputWid100` = 100% celda (no 111px). Tabla Datos Turno columnas compartidas; rowspan obs+prep. Disabled `#dadada`. Obs 450×116; prep 450×260; req/doc 360×202. | regla-paridad-ui-legacy v1.13 |
| 8 | ¿Playwright? | **e2e-migrado:** abrir infoTurno (asignar o sobreturno) muestra Datos Paciente + Datos Turno/Sobreturno. Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API nueva? | **No** v1. Reusa ficha + doc-req. | — |

**Fuera:** `infoPacienteTurno.xhtml` (otro dialog 1200×550).
