---
title: Spec — T5.1 hijo popups info north agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-popups
---

# Spec — Info popups north agenda

Padre: [`turnos-agenda-ficha-paciente/`](../turnos-agenda-ficha-paciente/) (D-TUR-24 cobrado; este hijo = gap north fa-info).  
Ruta: **`/turnos/agenda`** (no fork).

## Problema

T5.1 cableó buscadores y ficha. HIS tiene tres diálogos de **información de lectura** en north que no se renderizan:

1. `popupInfoBusqueda` — `fa-info` junto a lupa paciente (`agenda.xhtml` L40, L522–528).
2. `popupInfoConvenio` — `ui-icon-info` junto al combo plan (`agenda.xhtml` L75–78, L555–589).
3. `popupObservacionesPlanConvenio` — dialog 650px en el template padre (`asignacionTurnos.xhtml` L150–177). En **Agenda HIS no se abre solo**: `BBAgenda.showObservacionesConvenio()` nunca tiene callers; `setPacientesBuscados` usa `mostrarObs=false`; el ajax solo *refresca* el dialog si ya estuviera visible.

Call center no ve observaciones de convenio/plan ni la ayuda de búsqueda.

## Resultado (objetivo)

Misma fila north: info paciente + info plan; dialogs lectura; highlight del botón plan si hay observaciones (paridad `ui-state-error`).  
Datos: `ConvenioAgendaHit.descripcion` y `PlanConvenioOption.observaciones` (API T5.1 ya los trae). Sin endpoint nuevo salvo gap G2.

## Clarify — **FIRME** Camino 1 (2026-09-08)

Firma producto: **FIRME**. Gate UI G3 → Web G4.

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|---------|---------------------|-----------|
| 1 | ¿Misma ruta? | **Sí** `/turnos/agenda`. | T5 / T5.1 |
| 2 | ¿Info búsqueda? | **In scope** — botón info entre lupa y Limpiar. Dialog 978×618 header `informacion_de_busqueda`. Cuerpo = PNG `InfoBusquedaPac.png` (HIS `h:graphicImage`). Asset en `Hospital-Web/public/images/turnos/`. Cerrar = X del chrome (sin botón Cerrar en body). | `agenda.xhtml` L40 · L522–528 |
| 3 | ¿Info convenio (botón plan)? | **In scope** — botón info pegado al combo plan. Dialog 550×550 `informacion_convenio`: **siempre** panel Documentación requerida (tabla scroll 100px + emptyMessage + leyenda naranja Obligatorio) + paneles obs convenio/plan **solo si hay texto**. Cerrar = X chrome, **sin** Cerrar en body. Highlight si hay obs. | L75–78 · L555–589 · `infoConvenio` |
| 4 | ¿Auto-popup obs al elegir convenio/plan? | **WAIVE Agenda** — HIS **no** abre el dialog al buscar paciente/convenio ni al cambiar plan. Evidencia: `showObservacionesConvenio()` sin callers en HOSPITAL_2; `setPacientesBuscados(..., false)`; `actChangePlanConvenio` carga texto y hace `update` del dialog pero no pone `displayPopupObservacionesPlanConvenio=true`. Recepcion sí tiene `PF('$popupObservacionesPlanConvenio').show()` en `cabeceraRecepcion.xhtml` (otro CU). | `BBAgenda.java` L1672 · L1726 · L1187 |
| 5 | ¿Tabla documentación requerida? | **Chrome in scope** (panel + columnas + emptyMessage + Obligatorio). **Filas** **diferido** `turnos-agenda-elegibilidad-cobros` — `ts.doc_req_*` no en Flyway Api. Tabla vacía HIS; no fingir datos. | `listDocReq` · inventario DDL |
| 6 | ¿Elegibilidad / pagar? | **diferido** mismo hijo WS. | D-TUR-25 · P-ORA-010 |
| 7 | ¿API nueva? | **No** si descripcion/obs ya vienen. G2: verificar que north **persista** `hit.descripcion` al seleccionar convenio (hoy solo guarda el label). | `ConvenioAgendaHit` · `PlanConvenioOption` |
| 8 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Geometría: paciente `panelGrid` 4 cols (input\|lupa\|info\|X); plan 2 cols (select\|info). Botones 32×32 como lupa T5.1. | `regla-paridad-ui-legacy` |
| 9 | ¿Viaje Playwright? | **e2e-migrado**: abrir info búsqueda; abrir info convenio (fixture con obs o vacío → dialog). Legacy HIS **no**. | `turnos-agenda.spec.ts` |
| 10 | ¿Ciclo de vida / escritura? | **N/A** — solo lectura. Cerrar dialog no muta BD. | — |

**Fuera:** `popupInfoObservaciones` (observacionGral), `popUpInfoTurnosDeHoy` (T5 reserva), `popupInfoPaciente`, historial convenios, info prestación.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Help búsqueda paciente | `popupInfoBusqueda` · `InfoBusquedaPac.png` | **In scope** (PNG) | No |
| Info convenio (botón) | `infoConvenio` · `popupInfoConvenio` | **In scope** (chrome doc req + obs) | No |
| Auto obs convenio/plan | `popupObservacionesPlanConvenio` | **WAIVE** Agenda (HIS no lo dispara) | No |
| Highlight botón si hay obs | `ui-state-error` si descripcion/obs | **In scope** | No |
| Filas doc requerida | `listDocReq` | **diferido** elegibilidad | No (lectura) |
| Validador WS | `validarElegibilidad` | **diferido** | — |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Botón info paciente abre dialog `informacion_de_busqueda` con PNG HIS; X / Escape. |
| RF-2 | Botón info plan abre dialog `informacion_convenio` 550×550: panel Documentación requerida (vacío hasta elegibilidad) + obs convenio/plan si hay texto. |
| RF-3 | **WAIVE Agenda** — no auto-abrir observaciones al buscar/cambiar plan (HIS no lo hace). |
| RF-4 | Botón plan con highlight si hay obs (paridad visual aviso). |
| RF-5 | North conserva `descripcion` del convenio elegido (no solo label). |
| RF-6 | Gate UI: misma fila que lupa/plan; 32×32. |
| RF-7 | e2e: abrir ambos info. |
| NFR-1 | HTML obs: `innerHTML` acotado al fragmento HIS (`escape=false`); no ejecutar script. |
| NFR-2 | Output dialog ≠ `select` nativo (T5.1 lección). |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-36 | Slug = `turnos-agenda-info-popups` (hijo T5.1, no reabrir T5.1 gate) |
| D-TUR-37 | Filas `listDocReq` → elegibilidad (DDL). Chrome de tabla (vacía + Obligatorio) **este slice**. |
| D-TUR-38 | PNG búsqueda = `Hospital-Web/public/images/turnos/InfoBusquedaPac.png` |
| D-TUR-39 | Auto-popup `popupObservacionesPlanConvenio` en Agenda = **WAIVE** (HIS no lo dispara; evidencia BBAgenda sin callers de `showObservacionesConvenio`) |
