---
title: Spec — T6.2 hijo · PDF cola Reasignación
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-print
---

# Spec — PDF cola Reasignación de Turnos

Padre: [`turnos-agenda-cola-reasignar/`](../turnos-agenda-cola-reasignar/) (T6.2 **gate-done**).  
Ruta: **`/turnos/agenda`** accordion Reasignación de Turnos. **No** fork. **No** re-portar `TurnosAReasignar.rptdesign`.  
Clarify **FIRME Camino 1** — 2026-09-17. D-TUR-68. Francisco: «estoy hablando del imprimir de reasignar turno».

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md). Misma que T6.2.

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto) |
| Ramas `esClienteX()` | ninguna en `BBTurnosAReasignar.actBtnImprimir` |
| Decisión | sin ramas por instalación · resto `diferido(multi-instalacion)` |

## Problema

T6.2 dejó Imprimir south **disabled** con tooltip `diferido(turnos-agenda-cola-reasignar-print)`. El HIS `actBtnImprimir` manda los mismos filtros que Consultar a `TurnosAReasignar.rptdesign`. El diseño y `TURNOS.p_get_turnos_a_reasignar` **ya** están en Hospital-Reports.

## Resultado (objetivo Camino 1)

Consultar cola → Imprimir → PDF sidecar `TurnosAReasignar` con **las mismas filas** que la grilla JDBC (`ts.turno_a_reasignar`). Equipo vacío (D-TUR-17). Excel sigue diferido. **No** es el PDF de Consulta Agenda.

## Clarify — **FIRME Camino 1** (2026-09-17)

Firma producto: Francisco — «podes tomar ahora imprimir?» + «estoy hablando del imprimir de reasignar turno». Camino 1 = cable T5.5-pdf: GET Api → sidecar; no re-portar diseño; no Flyway; no seed de cola.

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T6.2 **gate-done**. Reports `TurnosAReasignar` portado. | verify T6.2 · CHECKLIST Reports |
| 2 | ¿Misma ruta / diseño? | **Sí** `/turnos/agenda` accordion. **No** re-migrar `.rptdesign`. **No** embeber BIRT. | `actBtnImprimir` · playbook 2026-09-08 |
| 3 | ¿Happy path? | Accordion → Consultar (hoy, mismo filtro) → Imprimir → PDF con paciente/centro/servicio/personal/prestación = grilla. Todos (ids `0`) y con centro. | HIS params + `NULLIF(id, 0)` en package |
| 4 | ¿Ciclo de vida? | **No** escribe `ts`. Cola nace en T4; este corte solo lee. | T6.2 |
| 5 | ¿Errores? | Fechas invertidas = `WRONG_INTERVAL_DATE_3`. Horas invertidas = `WRONG_INTERVAL_HOUR`. **No** `WRONG_INTERVAL_DATE_9` (paridad consultar cola). Sidecar caído + stub = **FAIL** G6. | `BBTurnosAReasignar` consultar |
| 6 | ¿Side-effects / DDL? | Flyway **n/a**. Default BIRT `idCentroAte=1` se pisa enviando `0` si Todos (package `NULLIF`). Equipo `codItemEquipo=""`. | `TurnosAReasignar.rptdesign` · `p_get_turnos_a_reasignar.sql` |
| 7 | ¿Paridad UI / Gate? | **N/A** pantalla nueva. South Imprimir ya T6.2 (150px). Encender el botón. | Gate UI padre cerrado |
| 8 | ¿Viaje Playwright? | **e2e-migrado** stub blob CI (enable Imprimir). G6 visual **sí** (motor BIRT, no mock). | [`regla-playwright-migracion.md`](../../../canon/regla-playwright-migracion.md) |
| 9 | ¿Fuera? | Excel. Equipo usable D-TUR-17. Re-port diseño. Consulta Agenda PDF. T6 padre. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Re-portar `.rptdesign` | Ya en Reports; DoR cable ≠ port |
| Omitir ids como T5.5 (`putOptionalId`) | Este diseño default `idCentroAte=1`; omitir filtraría centro 1. Camino 1 manda `0` (NULLIF) |
| PDF = ConsultaAgenda | Francisco: imprimir **de reasignar turno** |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Imprimir PDF cola = grilla | `BBTurnosAReasignar.actBtnImprimir` | **In scope** | No |
| Diseño `TurnosAReasignar.rptdesign` | HIS ReportManager | **hecho** Reports | No |
| Print-path `TURNOS.p_get_turnos_a_reasignar` | Oracle cursor | **portar** (ya sidecar SETOF; no clonar BODY) | No |
| Excel POI south | `actBtnExcel` | **hijo gate-done** `turnos-agenda-cola-reasignar-export` | No |
| Equipo north | combo HIS | **diferido** D-TUR-17 | No |

## Jobs / integraciones

`./tools/indice-legacy.sh --jobs turnos` — este CU es botón de pantalla. Ningún job del circuito de cola. Alta = T4. Vencidos = T6.

| Job / interfaz | Decisión |
|----------------|----------|
| Jobs cola reasignar | **N/A** (no hay tarea programada de print) |
| Integraciones externas | **N/A** (sidecar interno Reports) |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | GET `/api/v1/turnos/agenda/cola-reasignar/imprimir.pdf` → `reportId=TurnosAReasignar`. |
| RF-2 | Params HIS: fechas, horas, ids centro/servicio/personal/callCenter, labels, `usuario`. Equipo vacío. |
| RF-3 | Ids Todos / null / ≤0 → `"0"` (no `""`, no omitir: default BIRT 1). |
| RF-4 | Web: Imprimir south enabled; mismas validaciones que Consultar; blob download `turnos-a-reasignar-{fecha}.pdf`. |
| RF-5 | G6: PDF con valores = grilla cola (fixture `302675` si sigue vigente). |
| NFR-1 | Tiempo: GET p95 observado en G6 (no promedio). Volumen: filas de `ts.turno_a_reasignar` del dump (no vacío). Concurrencia: **N/A** (GET; HIS no `FOR UPDATE` en print). |
| NFR-2 | No re-portar layout. No BIRT en Api. |

## Acceso y auditoría

Mismo perfil de menú que T5/T6.2 (`/turnos/agenda`). Sin rol funcional extra en `actBtnImprimir`. Prueba negativa = padre T5 (no reabrir). No escribe tablas auditadas → auditoría **N/A**.

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-68 | Camino 1 hijo print cola: GET sidecar `TurnosAReasignar`; ids Todos = `0`; no Flyway; no re-port diseño; Excel fuera; G6 visual = grilla. |

## No objetivos

| Ítem | Destino |
|------|---------|
| Excel south | `turnos-agenda-cola-reasignar-export` |
| Equipo usable | D-TUR-17 |
| Re-migrar layout BIRT | **N/A** — ya en Hospital-Reports |
| PDF Consulta Agenda | T5.5-pdf **gate-done** |
| T6 padre | `turnos-ciclo-vida` |

## Evidencia (paths)

```
Hospital-HIS/.../BBTurnosAReasignar.java  actBtnImprimir
Hospital-Reports/src/main/resources/designs/TurnosAReasignar.rptdesign
Hospital-Reports/sql/packages-pg/turnos/p_get_turnos_a_reasignar.sql
Hospital-Api/.../GetColaReasignarPdfQueryHandler.java
Hospital-Web/.../turnos-cola-reasignar-vista.component.ts  imprimir()
```
