---
title: Spec — T5.1d cobros agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-cobros
---

# Spec — Cobros agenda (rechazo elegibilidad + pagar/saldo)

Padre: [`turnos-agenda-elegibilidad-cobros/`](../turnos-agenda-elegibilidad-cobros/).  
Ruta: **`/turnos/agenda`** (no fork).  
D-TUR-41.

## Problema

T5.1c Camino 1 deja el rechazo como **aviso** (icono + toast). En HIS el rechazo **cambia el convenio** y abre `$popUpObservacionesPacientes`.  
T5 no cableó `popUpInfoPagar`, saldo CTA ni filas coseguro en `infoTurno.xhtml`.

## Resultado (objetivo Camino 1)

Misma ruta: al rechazo seed, paridad del acto HIS (afiliado vacío + convenio/plan default + popup información).  
Dialogs pagar / saldo y filas coseguro en info turno: chrome HIS; montos solo si hay valor (`diferido(fixture)` si no hay seed de caja).

## Clarify — **FIRME** Camino 1 (2026-09-09)

Firma producto: **ok firme** (2026-09-09).

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|---------|---------------------|-----------|
| 1 | ¿Misma ruta? | **Sí** `/turnos/agenda`. | T5.1c |
| 2 | ¿Efecto rechazo (lo que “no deja hacer”)? | **In scope** — HIS: borra nro. afiliado; pone convenio/plan default (`ParamGeneral` / gap `call_center.id_convenio_dflt`); leyenda `LEYENDA_CONVENIO_DEFAULT`; abre `$popUpObservacionesPacientes` (modal, sin X). Autorizado: no cambia convenio. | `BBAgenda` ~2075–2100 · `asignacionTurnos.xhtml` L477–515 |
| 3 | ¿Contenido del popup rechazo? | **In scope** — estado / respuesta / cod error / descripción (si hay `mensajeValidador`); leyenda convenio default (rojo); observaciones paciente si hay texto; saldo CTA y “tiene prescripciones” **solo si hay dato** (no inventar filas). | xhtml L482–509 |
| 4 | ¿`popUpInfoPagar` al otorgar? | **In scope** — confirmación coseguro (“¿desea continuar?”) si el turno trae monto. Sin motor de caja. | `BBAsignacionTurnos` ~998–1007 · xhtml L446 |
| 5 | ¿`popUpSaldoCtaCtePaciente`? | **In scope** — dialog deuda + Aceptar si HIS lo dispara. Monto = lectura (seed/param), no asiento contable. | xhtml L462–475 |
| 6 | ¿Coseguro en infoTurno? | **In scope** — fila `saldo_cta_cte` / `coseguro_voluntario` / `coseguro_obligatorio` **si hay valor** (HIS `rendered`). Chrome + números; cálculo tarifario real → **diferido** (facturación, no este CU). | `infoTurno.xhtml` L97–123 |
| 7 | ¿WS / facturación packages? | **diferido** — no WAIVE. Este corte no cierra P-ORA-010 ni porta `Facturacion.java`. | P-ORA-010 |
| 8 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Dialogs HIS: header `Información`, `closable=false`, Aceptar. No `authPrimary` de más. | `regla-paridad-ui-legacy` |
| 9 | ¿Viaje Playwright? | **e2e-migrado:** (a) rechazo seed → popup + north pasa a convenio default; (b) autorizado no abre popup. Pagar/coseguro: e2e si hay fixture de monto; si no `diferido(fixture)`. Legacy HIS **no**. | `turnos-agenda.spec.ts` |
| 10 | ¿Prescripciones / caja / T6? | **Fuera** listar recetas reales y cobro en caja. Texto HIS si flag; resto diferido. | xhtml L508 |

**Fuera de este slice:** WS OS; ABM param_general; caja; T6; info prestación.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Rechazo → convenio default + limpia afiliado | `BBAgenda.validarElegibilidad` | **In scope** | No (solo UI/sesión) |
| Popup obs paciente | `$popUpObservacionesPacientes` | **In scope** | No |
| Leyenda convenio default | `LEYENDA_CONVENIO_DEFAULT` | **In scope** | No |
| Pagar coseguro (confirmar) | `$popUpInfoPagar` | **In scope** (copy + sí/no) | No |
| Dialog saldo CTA | `$popUpSaldoCtaCtePaciente` | **In scope** | No |
| Filas coseguro/saldo infoTurno | `infoTurno.xhtml` L97 | **In scope** (display) | No |
| Cálculo tarifario / caja | packages facturación | **diferido** | — |
| WS real OS | VALIDADORES | **diferido** P-ORA-010 | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Rechazo seed: north deja de usar esa OS (afiliado vacío + convenio/plan default). |
| RF-2 | Popup información con copy Resources; Aceptar cierra (`actionBtnCerrarMensajePaciente`). |
| RF-3 | Autorizado: no popup, no swap de convenio. |
| RF-4 | Gate UI xhtml **antes** de template. |
| RF-5 | e2e rechazo → popup + convenio default. |
| NFR-1 | Seed/montos demo ≠ facturación real. |
| NFR-2 | Resource delgado; sin SQL en Resource. |

## Decisiones (FIRME)

| Id | Decisión |
|----|----------|
| D-TUR-41 | Slug cobra popup rechazo + pagar/saldo/coseguro **display**. Motor facturación fuera. |
| D-TUR-43 | Convenio default v1: `ts.call_center.id_convenio_dflt` + `convenio.id_plan_conv_dflt_tur` (`ts.param_general` no está en Flyway). |
