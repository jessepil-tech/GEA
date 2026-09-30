---
title: Spec — T5.5 hijo · Excel consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export
---

# Spec — Exportar Excel Consulta Agenda

Padre: [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) (T5.5 **gate-done**).  
Ruta: **`/turnos/agenda`** vista consulta. **NO fork.**  
Clarify **FIRME Camino 1** — 2026-09-16 (Francisco: «habría que arrancar con ese tema exportar a excel»). D-TUR-63.

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md). Misma que T5.5.

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto) |
| Ramas `esClienteX()` | ninguna en `BBConsultaAgenda.generarReporteExcel` / `XLSParser` |
| Decisión | sin ramas por instalación · resto `diferido(multi-instalacion)` |

## Problema

T5.5 dejó **Exportar Excel** visible disabled + tooltip hijo. El HIS arma un `.xls` Apache POI (`XLSParser` HSSF) desde `listTurnos`, no un reporte del sidecar.

## Resultado (objetivo Camino 1)

Misma hoja. South **Exportar Excel** enabled. Consultar → Excel descarga `.xls` con título `Consulta Agenda`, cabecera de filtros (centro/servicio/profesional/fechas/horas/estado; **sin** convenio ni incluye-sobreturnos; **sin** equipo D-TUR-17) y las **16 columnas** de `generarReporteExcel`. Filas = JDBC `listarConsultaRango` (mismos filtros que Consultar). Lista vacía = **silencio** (HIS no hace nada).

## Clarify — **FIRME Camino 1** (2026-09-16)

Firma producto: Francisco pidió arrancar este hijo diferido de T5.5 (D-TUR-60).

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T5.5 **gate-done**. PDF hijo **gate-done**. Call center T5. | verify padre |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda` vista consulta. **NO fork.** **NO** Reports sidecar. | xhtml L214–216 |
| 3 | ¿Happy path? | Consultar (filas) → Exportar Excel → blob `.xls` (OLE HSSF) con mismas filas que la grilla + 16 cols HIS. Header con labels del north (ids Todos omiten esa fila de header, como HIS `if id != null`). | `generarReporteExcel` · `XLSParser.parse("Consulta Agenda", …)` |
| 4 | ¿Ciclo de vida? | **Solo lectura.** No INSERT/UPDATE. | BB no escribe |
| 5 | ¿Errores / permisos? | Fechas: mismas que Consultar (`WRONG_INTERVAL_DATE_3` / `_9`). Lista vacía: **no toast, no request** (HIS `if listTurnos empty` return). Call center sesión T5. 401/403 sin `/500`. | MessageBundle · BB L345 |
| 6 | ¿Side-effects? | **`ExcelExportPort`** (core) + adapter POI HSSF en infrastructure. Handler proyecta `ExcelSheet`. GET `/api/v1/turnos/agenda/consulta/exportar.xls`. Reusa `listarConsultaRango` + labels. **No** sidecar. **No** Flyway. Jobs: **N/A** (acto de pantalla). | `XLSParser` HSSF; umbral 65000→xlsx **fuera** (volumen piloto <<) |
| 7 | ¿Paridad UI / Gate? | **No** xhtml nuevo. South botón 120px ya T5.5: **quitar disabled**. Copy `exportar_excel` ya en labels. Chrome north/tabla **no** se retoca. | `consulta.xhtml` L214–216 · inventario geometría |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) botón enabled en vista consulta; (b) sin filas no dispara download; (c) tras Consultar fixture → download `.xls`. Legacy HIS **no**. G6 visual Excel **sí** (abrir el archivo). | `turnos-agenda.spec.ts` |
| 9 | ¿Fuera? | Emitter Excel del sidecar. Excel de otras pantallas. Equipo en header D-TUR-17. `mail_persona` tabla. Cliente-side SheetJS. T6. HOS-APP. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Emitter Excel del sidecar | Playbook: este botón es POI, no el diseño ConsultaAgenda |
| SheetJS en el browser | HIS es server-side `XLSParser`; re-resuelve labels; no es paridad |
| Clonar `XLSParser` entero (FacesUtils / tmp disk) | Tronco; este corte porta el **acto** Consulta Agenda |

**HIS in-memory vs re-query.** HIS exporta `listTurnos` del último Consultar. Camino 1: Web guarda `lastFiltro` al Consultar y el GET re-consulta ese filtro (no el north sucio).

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Exportar Excel Consulta Agenda | `consulta.xhtml` L214–216 · `generarReporteExcel` | **In scope** | No |
| `.xls` HSSF título + header + 16 cols | `XLSParser` + `TableClass` getters TmpTurno | **In scope** | No |
| Silencio si no hay filas | BB L345 | **In scope** | No |
| Imprimir PDF | T5.5 / T5.5-pdf | **fuera** (otro hijo) | No |
| Equipo en header | `equipo.getEquipoFull()` | **diferido** D-TUR-17 | — |
| Excel otras pantallas HIS | muchos `generarReporteExcel*` | **fuera** (no este slug) | — |
| Jobs programados | — | **N/A** (no hay job de Excel) | — |

## Firmas PL/SQL

Ninguna nueva. Consulta = JDBC T5.5 (equivalente `f_get_turnos_fecha`, **no clonar**). Decisión: **N/A** este hijo.

## Acceso

Mismo perfil de menú / call center que T5.5 Consulta Agenda. Rol funcional: el del padre (no hay Raise_application_error en `generarReporteExcel`). Prueba negativa: reusa T5.5 (actor sin call center).

## Presupuesto no funcional

| Eje | Objetivo | Cómo se mide |
|-----|----------|----------------|
| Tiempo | p95 del GET Excel ≤ p95 Consultar + 2 s en piloto | reloj G6 / IT |
| Volumen | mismo orden que la grilla del dump (hoy ~2–N filas `ts.turno`) | count filas xls = count grilla |
| Concurrencia | **N/A** — solo lectura; HIS no usa `FOR UPDATE` en este acto | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Botón Exportar Excel enabled en vista consulta (120px, copy HIS). |
| RF-2 | GET Api `.xls` HSSF; filename `consulta-agenda-YYYY-MM-DD.xls`. |
| RF-3 | Header: centro, servicio, personal, fecha/hora desde-hasta, estado — solo si hay valor. Sin convenio. Sin incluye-sobreturnos. Sin equipo. |
| RF-4 | Columnas: Fecha, Hora de Inicio, Hora Fin, Tipo Documento, Nro. Documento, Paciente, Nro. Historia Clínica Anterior, Centro Atención, Servicio, Profesional/Equipo, Código Prestación, Prestación, Convenio, Plan Convenio, Teléfono Paciente, Correo Paciente. |
| RF-5 | Filas = `listarConsultaRango` (incluyeSobreturnos + convenio sí filtran datos). Profesional/Equipo = `personal` (equipo D-TUR-17). |
| RF-6 | 0 filas: Web no llama; Api 204. Sin toast. |
| RF-7 | e2e-migrado: enabled + download stub `.xls`. G6: abrir archivo con valores = grilla. |
| NFR-1 | POI solo en infrastructure. Resource delgado. No motor de reportes en Api. |
| NFR-2 | Sin Flyway. Sin fork. |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-63 | Camino 1 hijo Excel: POI HSSF en Api; GET `exportar.xls`; reusa JDBC T5.5; botón south live; no sidecar; no SheetJS; umbral xlsx 65k fuera. |

## No objetivos

| Ítem | Destino |
|------|---------|
| PDF ConsultaAgenda | **fuera** `turnos-agenda-consultas-pdf` |
| Equipo usable | D-TUR-17 |
| Excel T4 / otras pantallas | fuera |
| Tabla `mail_persona` | vacío; no hijo |
| T6 | `turnos-ciclo-vida` |

## Evidencia (paths)

```
Hospital-Legacy/.../consulta.xhtml L214–216
Hospital-Legacy/.../BBConsultaAgenda.java generarReporteExcel
Hospital-Legacy/.../utils/poi/XLSParser.java HSSF < 65000 filas
Hospital-Api GET /api/v1/turnos/agenda/consulta/exportar.xls
```
