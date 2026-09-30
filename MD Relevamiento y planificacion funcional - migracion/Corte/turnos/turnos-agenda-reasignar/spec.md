---
title: Spec — T6.1 reasignar agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-reasignar
---

# Spec — Reasignar turno desde agenda

Padres: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (**D-TUR-26**) · A8 [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (T6 padre `turnos-ciclo-vida` **diferido**).  
Ruta: **`/turnos/agenda`** (no fork).  
D-TUR-26 · D-TUR-44.

## Problema

T5 cobra liberar simple. El acto HIS **Reasignar** (menú fila) no está en Web: sesión `TurnoAReasignar`, north del paciente origen, overlay `PENDIENTE_LIBERAR` y cierre al otorgar un `LIBRE` que libera el origen.

## Resultado (objetivo Camino 1)

Misma ruta: menú fila **REASIGNAR** / **CANCELAR REASIGNACIÓN**; al cerrar, el paciente queda en el slot nuevo y el origen se libera (HIS `otorgarTurnoPac(turno, turnoAReasignar)`).

## Clarify — **FIRME** Camino 1 (2026-09-09)

Firma producto: **ok firme** (2026-09-09).

| # | Pregunta | Respuesta FIRME | Evidencia |
|---|---------|-----------------|-----------|
| 1 | ¿Pipeline previo? | T5 **gate-done** (otorgar/liberar/grilla). Call center T1. Hab T2. Oferta T4. | A7 T5 · A8 este hijo |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. **NO fork.** | T5 D-TUR-22 |
| 3 | ¿Happy path? | HIS menú fila **REASIGNAR** (`bbAgenda.actBtnReemplazarTurno`) + **CANCELAR_REASIGNACION**. Click Reasignar: sesión `TurnoAReasignar`; north = paciente del turno; toast `TURNO_PENDIENTE_LIBERAR`; origen pintado `PENDIENTE_LIBERAR` (**estado UI, NO columna `ts.turno`**). Cancelar: limpia sesión. Cierre: asignar un **LIBRE** al **mismo paciente** → popup observaciones reasignar (`$popupObservacionesReasignarTurno` si HIS lo pide) → `otorgarTurnoPac(turno, turnoAReasignar)` libera origen. | `agenda.xhtml` L279–284 · `BBAgenda.actBtnReemplazarTurno` ~2420 · overlay ~861 · `BBAsignacionTurnos` otorgar ~1112 |
| 4 | ¿Ciclo de vida / overlay? | Overlay solo mientras hay sesión y `!isTurnoAReasignar()` (flujo agenda, no cola). Al consultar, HIS puede restaurar `OTORGADO` en el DTO de pintura; persistencia origen sigue `OTORGADO` hasta el cierre. Cancelar quita sesión y overlay. | `BBAgenda` ~861–868 · ~1805–1808 · `actBtnCancelarReasignacionTurno` ~2440 |
| 5 | ¿Errores / permisos? | Mismos gates T5 (call center, `modificable`, paciente en fila). Reasignar disabled si no modificable o sin paciente. Toast/error API **sin** `/500`. | xhtml L279–281 |
| 6 | ¿Side-effects? | Mail/SMS/BIRT → **T7** (no este CU). `hist_turno` del otorga/libera T5 se reutiliza al cerrar; **origen** con `pf_hist_turno(..., 'S')` → estado **`REASIGNADO`** (no copiar `OTORGADO`). Pantalla hist + job vencidos → padre T6. **Delta G6 2026-09-18:** ops vio origen+destino OTORGADO; el destino está bien; el origen no. Cola T6.2 no marca REASIGNADO (no hay origen). | T7 · `turnos-ciclo-vida` · BODY `pf_hist_turno` · `ImpBusTurno` L524 `esReasignacion=true` |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Gate UI xhtml **ANTES** de template. Popup: header `observaciones`, `closable=false`, width 650, Aceptar/Cancelar. Menú gear: REASIGNAR vs CANCELAR según overlay. No `authPrimary` extra. | `regla-paridad-ui-legacy` · `asignacionTurnos.xhtml` L122–147 |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) reasignar → origen pendiente + north paciente; (b) cancelar limpia; (c) asignar slot nuevo libera origen. Legacy HIS **no**. | `regla-playwright-migracion.md` |
| 9 | ¿Cola / suspender / reemplazo / vencidos? | **Fuera** (hijos, **no WAIVE**): cola `turnosAReasignar.xhtml` (T4 eliminar grilla); `suspenderGrillaTurnos`; `reemplazoPersonalGrillaTurnos`; job vencidos / hist; T7. | A8 resto → `turnos-ciclo-vida` |

**Fuera de este slice:** cola T4; suspender; reemplazo profesional; vencidos; hist pantalla; T7; fork de ruta.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Menú REASIGNAR → sesión + north + toast + overlay | `actBtnReemplazarTurno` · L279 | **In scope** | No (sesión UI) |
| Menú CANCELAR_REASIGNACION | `actBtnCancelarReasignacionTurno` · L282 | **In scope** | No |
| Overlay `PENDIENTE_LIBERAR` | `initTurnos` ~861 | **In scope** (UI) | **No** columna `ts.turno` |
| Popup obs reasignar | `$popupObservacionesReasignarTurno` | **In scope** (si HIS lo pide) | No (texto al otorga) |
| Cierre: otorga LIBRE + libera origen | `otorgarTurnoPac(turno, turnoAReasignar)` | **In scope** | Sí (reusa T5 otorga/libera) |
| Cola `turnosAReasignar.xhtml` | `BBTurnosAReasignar` | **diferido** T6 | — |
| Suspender grilla | `suspenderGrillaTurnos` | **diferido** T6 | — |
| Reemplazo profesional | `reemplazoPersonalGrillaTurnos` | **diferido** T6 | — |
| Job vencidos / hist pantalla | `f_migra_turno_vencido` · `BBHistorialTurno` | **diferido** T6 | — |
| Mail/SMS/BIRT | `GeneraMailsTurno` · `Turno.rptdesign` | **diferido** T7 | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Reasignar (fila con paciente, modificable): sesión origen; north = ese paciente; toast `TURNO_PENDIENTE_LIBERAR`; origen pintado `PENDIENTE_LIBERAR` (solo UI). |
| RF-2 | Cancelar reasignación: limpia sesión; overlay desaparece; north no queda atado al acto. |
| RF-3 | Cierre: reservar/otorgar un `LIBRE` al **mismo** paciente; popup obs si sesión origen y HIS lo pide; Aceptar continúa otorga; `otorgarTurnoPac` libera origen. Cancelar popup cierra el dialog (sesión origen **sigue**). |
| RF-4 | Gate UI xhtml **antes** de template. Inventarios G0. |
| RF-5 | e2e-migrado: reasignar → pendiente + north; cancelar limpia; asignar slot nuevo libera origen. |
| NFR-1 | `PENDIENTE_LIBERAR` **no** se persiste en `ts.turno`. |
| NFR-2 | Resource delgado; sin SQL en Resource. Reusar comandos T5; extender otorga con origen opcional. |
| NFR-3 | Sin fork `/turnos/agenda`. Error API sin `/500`. |

## Decisiones (FIRME)

| Id | Decisión |
|----|----------|
| D-TUR-26 | Reasignar **menú fila agenda** → este hijo (`turnos-agenda-reasignar`). Liberar simple queda en T5. Cola / suspender / reemplazo / vencidos / hist → padre T6 `turnos-ciclo-vida`. |
| D-TUR-44 | Camino 1: overlay UI-only; cierre = otorga T5 + libera origen; popup obs si HIS lo pide; e2e-migrado 3 viajes; T7 fuera. |

## No objetivos

| Ítem | Destino |
|------|---------|
| Liberar simple (motivo) | T5 (hecho) |
| Cola tras eliminar grilla | T6 / T4 ya inserta `turno_a_reasignar` |
| Suspender / reemplazo profesional | `turnos-ciclo-vida` |
| Job vencidos / ABM hist | `turnos-ciclo-vida` |
| Mail/SMS/BIRT | T7 |

## Evidencia legacy (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/agenda.xhtml L279–284
Hospital-Legacy/HOSPITAL_2/src/.../BBAgenda.java actBtnReemplazarTurno ~2420; PENDIENTE_LIBERAR overlay ~861
Hospital-Legacy/HOSPITAL_2/src/.../BBAsignacionTurnos.java otorgar ~1112 $popupObservacionesReasignarTurno
Hospital-Legacy/HOSPITAL-BUSINESS/src/.../MessageBundle.java TURNO_PENDIENTE_LIBERAR
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/asignacionTurnos.xhtml L122–147
```
