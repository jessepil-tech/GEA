---
title: Spec — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-consultas
---

# Spec — Consulta Agenda (Turnero)

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/).  
Ruta: **`/turnos/agenda`** (no fork).  
Clarify **FIRME** Camino 1 — 2026-09-14 (Francisco: opción 1 Imprimir en T5.5). D-TUR-59 · D-TUR-60.

## Problema

T5–T5.4-b cubren Agenda / Sobreturno / Múltiples. El ítem Turnero **Consulta Agenda** sigue `disabled` + tooltip `diferido(turnos-agenda-consultas)`. En HIS es otra hoja del mismo shell (`consulta.xhtml` · `BBConsultaAgenda`): filtros por rango y grilla de `turno`, no el calendario del día.

## Resultado (objetivo Camino 1)

Misma ruta. Accordion 170px. Click **Consulta Agenda** → vista consulta (sin west calendario 260). North HIS → Consultar → tabla. Info fila reusa infoTurno T5. South: **Imprimir live** (cable sidecar `ConsultaAgenda`); **filas/valores del PDF** → hijo [`turnos-agenda-consultas-pdf`](../turnos-agenda-consultas-pdf/); **Excel** visible disabled + hijo. Equipo combo **visible disabled** D-TUR-17.

## Clarify — **FIRME Camino 1** (2026-09-14)

Firma producto: Francisco «ok» al corte + **opción 1** Imprimir en T5.5 (Excel diferido).

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T5 **gate-done**. Call center T1. Oferta T4. Combos centro/servicio T5. | A7 T5 |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. **NO fork.** Accordion 170 **sí**. West calendario Agenda **oculto** en esta vista (no está en `consulta.xhtml`). | `consulta.xhtml` composition · `asignacionTurnos.xhtml` L79–81 |
| 3 | ¿Happy path? | Turnero **Consulta Agenda** → north: centro, servicio, profesional+lupa (tipeable), equipo disabled, estado (Todos/Libre/Otorgado), convenio+lupa tipeable, fecha desde/hasta (default hoy, `mindate` hoy), hora 00:00–23:59, check incluye sobreturnos **true**. Consultar → POST/GET rango → tabla scroll. Click info → infoTurno T5. Volver a Agenda: ítem Agenda. | xhtml L18–223 · `BBConsultaAgenda` ctor + `actBtnConsultar` |
| 4 | ¿Ciclo de vida? | **Solo lectura** `ts.turno`. No INSERT/UPDATE en Consultar. Otorgar/liberar = T5 si el usuario abre infoTurno (reuso; no ensanchar). | BB solo `selectTurnosEntreFechasFiltro` |
| 5 | ¿Errores / permisos? | `fechaHasta` posterior a `fechaDesde` → `WRONG_INTERVAL_DATE_3`. `fechaDesde` ≥ hoy → `WRONG_INTERVAL_DATE_9`. Call center sesión T5. Toast **sin** `/500`. Filtros vacíos = Todos (HIS combo Todos). | MessageBundle 2256 / 2262 |
| 6 | ¿Side-effects? | **Imprimir cable in-scope:** Api `ReportsPort` → sidecar `reportId=ConsultaAgenda` (diseño **ya** en Hospital-Reports; no re-migrar `.rptdesign`). Params HIS `actBtnImprimir` (ids + labels + fechas/horas + `estadoTurno`; **sin** convenio ni incluyeSobreturnos — paridad HIS). Equipo vacío D-TUR-17. **G6 visual PDF con valores** (grilla OK / columnas vacías 2026-09-14) → **diferido** [`turnos-agenda-consultas-pdf`](../turnos-agenda-consultas-pdf/). **Excel POI** → **diferido** [`turnos-agenda-consultas-export`](../turnos-agenda-consultas-export/) (botón visible disabled). Mail/SMS **N/A**. No embeber BIRT en Api. | xhtml L217–219 · `ConsultaAgenda.json` · T4 patrón `GetConsultaAgendaGeneradasPdf*` |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Labels col **111px**; `InputWid100`; profesional/convenio tipeable+lupa (paridad agenda 2026-09-14); fechas+horas misma geometría HIS (fecha desde/hasta **misma celda**; horas+check **misma fila**). Tabla cols HIS. Footer leyenda 5 estados. Look Origin / geometría HIS. | `regla-paridad-ui-legacy` |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) Turnero abre vista; (b) fechas invertidas → toast; (c) Consultar → filas fixture; (d) Imprimir → stub PDF CI (patrón T4). **G6 visual PDF con valores** = `diferido(turnos-agenda-consultas-pdf)`. Legacy HIS **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿Fuera? | Equipo usable **D-TUR-17**. Pre-agenda. T6. Avisos. Lista espera (`personalListaEspera`). `consultaTurnos*.xhtml`. T4 agendas generadas. HOS-APP. | slugs abajo |

**Accordion Turnero (esta hoja)**

| Ítem | Este corte |
|------|------------|
| Agenda | Live; sale de consulta |
| Sobreturno | Live T5.2 (popup; HIS lo deshabilita si `idPMI ne Agenda` — Web: al estar en consulta, **disabled** como HIS) |
| Múltiples | Live T5.4 |
| Consulta Agenda | **Live** (esta hoja, highlight) |
| Avisos | Disabled HIS |
| Reasignación | Disabled T6 |
| Pre-agenda | Live T5.6 [`turnos-agenda-preagenda`](../turnos-agenda-preagenda/) |
| Historial | Disabled T6 |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Turnero Consulta Agenda | `asignacionTurnos.xhtml` L79–81 | **In scope** | No |
| North filtros + Consultar | `consulta.xhtml` L18–135 | **In scope** | No |
| Tabla turnos rango | L142–207 · `f_get_turnos_fecha` | **In scope** (JDBC `ts.turno`, no clonar PL/SQL) | No |
| Info turno | L149–150 `actBtnInfoTurno` | **In scope** reusa T5 | No (consulta); T5 si otorga |
| Leyenda estados | L200–206 | **In scope** | No |
| Excel | L214–216 POI | **diferido** `turnos-agenda-consultas-export` | — |
| Imprimir BIRT cable | L217–219 `ConsultaAgenda.rptdesign` | **In scope** cable sidecar (diseño ya en Reports) | No |
| Imprimir PDF con valores | mismo acto; G6 visual = grilla | **done** hijo [`turnos-agenda-consultas-pdf`](../turnos-agenda-consultas-pdf/) | No |
| Equipo combo usable | L56–61 | **diferido** D-TUR-17 (visible disabled) | — |
| Lista espera prefill | ctor `PersonalListaEspera` | **fuera** | — |
| Pre-agenda | `preAgendaTurnos.xhtml` | **done** hijo [`turnos-agenda-preagenda`](../turnos-agenda-preagenda/) | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Turnero Consulta Agenda enabled; vista en `/turnos/agenda`; sin calendario 260. |
| RF-2 | North HIS; defaults hoy / 00:00–23:59 / incluye sobreturnos. |
| RF-3 | Consultar lista `ts.turno` con filtros (centro, servicio, personal, estado, convenio, fechas, horas, incluye sobreturno). Equipo ignorado (D-TUR-17). |
| RF-4 | Info fila abre infoTurno T5. |
| RF-5 | Toasts intervalo fecha HIS. |
| RF-6 | Gate UI xhtml **antes** de template. |
| RF-7 | e2e-migrado: abre vista + toast fechas + filas + Imprimir stub PDF. |
| RF-8 | Excel visible disabled + tooltip hijo. Sobreturno disabled en esta vista (HIS). |
| RF-9 | Imprimir live (cable): GET PDF Api → sidecar `ConsultaAgenda`; descarga blob Web. No embeber BIRT. **Filas/valores** = hijo `turnos-agenda-consultas-pdf`. |
| NFR-1 | Resource delgado; JDBC `ts`; no clonar `f_get_turnos_fecha`. |
| NFR-2 | Sin fork. Error API sin `/500`. ReportsPort; no motor BIRT en Api. |

## Decisiones (Camino 1)

| Id | Decisión |
|----|----------|
| D-TUR-59 | Slug `turnos-agenda-consultas` = hoja `consulta.xhtml` en el shell Agenda. No T4. No `consultaTurnos.xhtml`. |
| D-TUR-60 | Camino 1: misma ruta; oculta calendario; JDBC equivalente a `f_get_turnos_fecha`; **Imprimir** = cable sidecar `ConsultaAgenda` (no re-portar diseño); **G6 visual PDF** diferido `turnos-agenda-consultas-pdf`; **Excel** diferido; equipo D-TUR-17. |

## No objetivos

| Ítem | Destino |
|------|---------|
| Excel POI | `turnos-agenda-consultas-export` |
| PDF ConsultaAgenda con filas/valores | `turnos-agenda-consultas-pdf` |
| Re-migrar `ConsultaAgenda.rptdesign` | **N/A** — ya en Hospital-Reports |
| Equipo usable | D-TUR-17 |
| Pre-agenda | `turnos-agenda-preagenda` |
| T6 hist / cola | `turnos-ciclo-vida` |
| T4 agendas generadas | hecho |

## Evidencia legacy (paths)

```
Hospital-Legacy/.../asignacionTurnos.xhtml L79–81
Hospital-Legacy/.../consulta.xhtml
Hospital-Legacy/.../BBConsultaAgenda.java actBtnConsultar · actBtnImprimir
Hospital-Legacy/HOSPITAL-BUSINESS/.../Turnos.hbm.xml fTurnosEntreFechasFiltro → TS.TURNOS.f_get_turnos_fecha
Hospital-Legacy/HOSPITAL-BUSINESS/.../MessageBundle.java WRONG_INTERVAL_DATE_3 · WRONG_INTERVAL_DATE_9
```
