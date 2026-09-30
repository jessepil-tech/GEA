---
title: Spec — T5.1c elegibilidad north + doc req
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros
---

# Spec — Elegibilidad north agenda + filas doc req

Padre: [`turnos-agenda-info-popups/`](../turnos-agenda-info-popups/).  
Ruta: **`/turnos/agenda`** (no fork).  
D-TUR-25 / P-ORA-010.

## Problema

T5.1 dejó nro. afiliado / documento **disabled** y el icono validador **oculto**.  
T5.1b dejó la tabla Documentación requerida **vacía** (chrome sí; filas no).  
Call center no puede validar obra social ni ver docs del plan.

## Resultado (objetivo Camino 1)

Misma fila north HIS: afiliado (máscara) + icono estado; documento editable solo si el validador pide doc; spinner `validando_elegibilidad`; decisión vía **seed** (mismo puerto AGI).  
Info convenio: filas reales desde `ts.doc_req_plan_conv` ∪ `ts.doc_req_prest_plan`.

## Clarify — **FIRME** Camino 1 (2026-09-08)

Firma producto: **FIRME**. G1–G6 **gate-done** 2026-09-10.

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|---------|---------------------|-----------|
| 1 | ¿Misma ruta? | **Sí** `/turnos/agenda`. | T5 / T5.1 / T5.1b |
| 2 | ¿North afiliado / doc / icono / spinner? | **In scope** — `inputMask` afiliado habilitado si `controlElegibilidad` o `validaNroDocumento`; icono `fa-check-circle` / `fa-times-circle` / `fa-plug` / `fa-exclamation-circle` + title `PENDIENTE` o estado WS; dialog modal `$validandoElegibilidad` (gif → spinner DS). Documento: editable + icono solo si `validaNroDocumento`; si no, disabled (paridad T5.1). | `agenda.xhtml` L82–115 · L641–644 |
| 3 | ¿WS real P-ORA-010? | **diferido** — no WAIVE. Este corte reusa `ValidadorElegibilidadPort` + `elegibilidad_seed` (paridad de **decisión**, no de transporte). Clientes `Hospital-Legacy/VALIDADORES/` quedan en P-ORA-010. | `SeedValidadorElegibilidadAdapter` · `pendientes-solo-oracle.md` |
| 4 | ¿Cuándo se valida? | **In scope** — `change` de afiliado o doc (si aplica), mismo disparo HIS `validarElegibilidad()`. Convenio con `req_valid_elegibilidad` / `id_validador_online` (HIS `convenioValidElegibilidad`). Sin flag → campos disabled, sin icono (como hoy). | `BBAgenda.validarElegibilidad` · `ValidadorWS.convenioValidElegibilidad` |
| 5 | ¿Filas documentación requerida? | **In scope** — Flyway `ts.doc_requerido`, `ts.doc_req_plan_conv`, `ts.doc_req_prest_plan` (nombres Oracle TS; Reports hoy en `public.*`). GET para el dialog info convenio ya cableado. Filtro HIS: `id_convenio` + `id_plan_convenio` + `req_paciente='S'`; prestación suma `doc_req_prest_plan` por `cod_prestacion`. Naranja `obligatorio_recep`. | `initInfoConvenio` / `initInfoConvenioPrestacion` · seed Reports |
| 6 | ¿Cobros (pagar / saldo CTA / coseguro)? | **diferido** `turnos-agenda-cobros` — `popUpInfoPagar`, `popUpSaldoCtaCtePaciente`, bloques saldo/coseguro en `infoTurno.xhtml`. Otra superficie; packages facturación. No silencio. | `asignacionTurnos.xhtml` L446 · `infoTurno.xhtml` L97 |
| 7 | ¿Popup observaciones paciente al rechazo? | **Camino 1 mínimo:** dialog/toast con `mensajeValidador` (autorizado/rechazo/error conexión). El popup HIS `$popUpObservacionesPacientes` (saldo + prescripciones + convenio default) → **mismo hijo cobros**. | `BBAgenda` ~2098 · `asignacionTurnos.xhtml` ~498 |
| 8 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Fila afiliado: input `InputWid100` + icono **misma fila** (panelGrid 2 cols). Fila documento igual. Spinner modal. No `authPrimary`. Máscara desde `plan_convenio.mascara_nro_afi` (`#`→dígito). | `regla-paridad-ui-legacy` |
| 9 | ¿Viaje Playwright? | **e2e-migrado:** (a) convenio con flag + seed autorizado → icono OK; (b) seed rechazo → icono error + mensaje; (c) info convenio con fixture doc req → filas. Legacy HIS **no**. Mocks ≠ G6. | `turnos-agenda.spec.ts` |
| 10 | ¿Info prestación / historial convenios? | **Fuera** (igual T5.1b). | `popupInfoPrestacion` |

**Fuera de este slice:** WS HTTP real; pagar; CTA CTE; coseguro en otorga; T6; info prestación.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Habilitar afiliado si convenio valida | `controlElegibilidad` | **In scope** | No |
| Máscara nro afiliado | `plan_convenio.mascara_nro_afi` | **In scope** | No |
| Validar on change | `validarElegibilidad` | **In scope** (seed) | No |
| Icono + title estado | fa-check / times / plug / exclamation | **In scope** | No |
| Spinner validando | `$validandoElegibilidad` | **In scope** | No |
| Mensaje rechazo / error | `mensajeValidador` | **In scope** (dialog/toast mínimo) | No |
| Filas doc req en info convenio | `listDocReq` | **In scope** | No (lectura) |
| WS real OS | `ValidadorWS` + VALIDADORES | **diferido** P-ORA-010 | — |
| Pagar / saldo / coseguro | `popUpInfoPagar` · infoTurno | **diferido** [`turnos-agenda-cobros/`](../turnos-agenda-cobros/) | — |
| Observaciones paciente + CTA al rechazo | `$popUpObservacionesPacientes` | **diferido** [`turnos-agenda-cobros/`](../turnos-agenda-cobros/) | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Si el convenio exige validador: afiliado editable + máscara; si no, disabled (paridad actual). |
| RF-2 | Change afiliado (o doc si `validaNroDocumento`) llama validar; spinner mientras tanto. |
| RF-3 | Icono verde / rojo / naranja / pendiente según resultado seed; title = estado o `PENDIENTE`. |
| RF-4 | Convenio sin flag: sin icono (rendered HIS `controlElegibilidad`). |
| RF-5 | GET doc req llena la tabla del dialog info convenio; emptyMessage si no hay filas. |
| RF-6 | Gate UI: misma fila input+icono; copy Resources. |
| RF-7 | e2e: autorizado, rechazo, filas doc req. |
| NFR-1 | Seed ≠ WS real; P-ORA-010 no se cierra. |
| NFR-2 | DDL `ts` (no `public.doc_req_*` de Reports). |
| NFR-3 | Resource delgado; puerto en application; sin SQL en Resource. |

## Decisiones (FIRME)

| Id | Decisión |
|----|----------|
| D-TUR-40 | Slug cobra D-TUR-25 **parcial**: UI+seed+doc req. WS real = P-ORA-010. |
| D-TUR-41 | Cobros → hijo [`turnos-agenda-cobros/`](../turnos-agenda-cobros/) (cola corta; Clarify PROPUESTO 2026-09-09). |
| D-TUR-42 | Oráculo v1 = `elegibilidad_seed` (mismo adapter AGI, contrato agenda). |
