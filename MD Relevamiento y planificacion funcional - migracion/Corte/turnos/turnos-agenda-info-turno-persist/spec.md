---
title: Spec — persist obs + fecha prescripción
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist
---

# Spec — Persist al otorgar (infoTurno)

Padre: [`turnos-agenda-info-turno/`](../turnos-agenda-info-turno/) (chrome **gate-done**; persist este corte).  
Ruta: **`/turnos/agenda`**.  
**FIRME** Camino 1 — 2026-09-10 (Francisco: «ok firme»). **gate-done** G6 **PASS** 2026-09-10 («ok»). D-TUR-47 · D-TUR-48.

## Problema

El modal HIS escribe `ts.turno.observaciones` y `fecha_prescripcion` al **Asignar**. T5.1e dejó el chrome; el POST otorgar T5 solo cambia estado. Información de un OTORGADO muestra esos campos vacíos.

## Resultado (objetivo Camino 1)

Misma ruta. Al Asignar: persistir textarea + fecha; validar como HIS (toasts + popup confirma si aplica). Al abrir Información: mostrar lo grabado.

## Clarify — **FIRME** Camino 1 (2026-09-10)

| # | Pregunta | Respuesta | Evidencia |
|---|---------|-----------|-----------|
| 1 | ¿Pipeline? | T5 otorga + T5.1e chrome **gate-done**. Columnas `ts.turno.observaciones` / `fecha_prescripcion` V31. `servicio_centro.req_fecha_prescrip_amb` V38. `plan_convenio.req_ctrl_fecha_prescrip` + `ctd_max_dias_prescrip` V31. | padres · Flyway |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. Mismo dialog T5.1e. LIBRE, sobreturno y reasignar. | `infoTurno.xhtml` |
| 3 | ¿Happy path? | Modo otorga: textarea y calendar editables. Asignar → `POST …/otorgar` con `observaciones` + `fechaPrescripcion` → UPDATE esas cols + estado OTORGADO (T5). Información: GET grilla trae las dos cols. | BB `actionBtnOtorgarTurno` L1110–1129 · textarea `turnoInfo.observaciones` |
| 4 | ¿Ciclo de vida? | Se escriben **al otorgar**, no al reservar. Cancelar Asignar no persiste. Liberar: igual T5 (DELETE si sobreturno). Truncar obs **250**. | V31 `varchar(250)` |
| 5 | ¿Errores? | Fecha > hoy → toast `FECHA_PRESCRIPCION_POSTERIOR_ACTUAL`. Si `req_fecha_prescrip_amb='S'` y fecha vacía → toast `FECHA_PRESCRIPCION_REQUIRED`. Si req + `req_ctrl_fecha_prescrip` y fecha anterior a hoy−`ctd_max_dias_prescrip` → toast `FECHA_PRESCRIPCION_SUPERA_CANTIDAD_DIAS`. Popup `$popUpConfirmaTurnoPrescripcion` si HIS lo dispara (vencida / sin fecha con req). Toast sin `/500`. | BB L950–992 · MessageBundle · xhtml L287 |
| 6 | ¿Fuera? | Prep/req filas → `turnos-agenda-info-turno-prest`. Repetidos/múltiples. Tipo paciente. No ALTER. | T5.1e |
| 7 | ¿Gate UI? | Chrome infoTurno **ya**. Este corte: calendar editable (HIS `p:calendar` dd/MM/yy + botón; Web `type=date` + max hoy) + dialog confirma header `Confirmación`, `closable=false`, copy `prescripcion_vencida` / `no_ha_ingresado_fecha_prescripcion` + `¿Desea continuar?` Aceptar/Cancelar. Disabled `#dadada`. | `regla-paridad-ui-legacy` v1.13 |
| 8 | ¿Playwright? | **e2e-migrado:** Asignar con obs+fecha → Información las muestra. Fecha futura → toast. Popup confirma Aceptar. Required: **e2e-migrado** si hay fixture `req_fecha_prescrip_amb`; si no `diferido(fixture)`. Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API? | Extender **mismo** `POST …/otorgar` (body). Extender **mismo** GET grilla (dos campos). Sin Resource nuevo. Flag `confirmaPrescripcion` = sesión HIS. | scaffold |

**Camino 1** = persist + lectura + validaciones HIS un-turno (toasts + popup confirma).  
No hay Camino “solo grabar sin validar”: HIS bloquea.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Obs al otorgar | `turnoInfo.observaciones` → SP otorga | **In scope** | UPDATE `ts.turno.observaciones` |
| Fecha prescripción al otorgar | `fechaPrescripcion` → `turnoInfo` → SP | **In scope** | UPDATE `fecha_prescripcion` |
| Leer en Información | `turnoInfo` del SELECT | **In scope** | GET grilla |
| Fecha no futura | BB L950 | **In scope** | No |
| Fecha required si centro/servicio lo pide | `reqFechaPrescripAmb` L971 | **In scope** | No |
| Tope días plan | L975 | **In scope** | No |
| Popup confirma vencida / vacía | `$popUpConfirmaTurnoPrescripcion` | **In scope** | No (sesión HIS; Web: flag del POST) |
| Obs/fecha por fila repetidos | xhtml L164 | **diferido** `turnos-agenda-repetidos` | — |
| Prep / requisitos | `preparacionPrest` | **diferido** `turnos-agenda-info-turno-prest` | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Asignar persiste obs (≤250) y fecha (nullable salvo required). |
| RF-2 | Información de OTORGADO muestra esos valores. |
| RF-3 | Mismos tres toasts MessageBundle. |
| RF-4 | Popup confirma HIS si el acto lo dispara; Aceptar sigue otorga; Cancelar vuelve al infoTurno. |
| RF-5 | Gate UI dialog confirma **antes** de template. |
| RF-6 | e2e persist + toast futura. |
| NFR-1 | Sin endpoint nuevo. Resource delgado. |

## Decisiones **FIRME**

| Id | Decisión |
|----|----------|
| D-TUR-47 | Persist al **otorgar** (no al reservar). |
| D-TUR-48 | Lectura vía grilla día (no GET infoTurno). |
