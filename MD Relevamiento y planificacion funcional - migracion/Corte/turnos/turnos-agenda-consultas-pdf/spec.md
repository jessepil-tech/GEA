---
title: Spec — T5.5 hijo · PDF consulta agenda con filas
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-pdf
---

# Spec — PDF Consulta Agenda (valores = grilla)

Padre: [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) (T5.5 **gate-done**).  
Ruta: **`/turnos/agenda`** vista consulta. **No** fork. **No** re-portar `ConsultaAgenda.rptdesign`.  
Clarify **FIRME Camino 1** — 2026-09-15 (Francisco: Flyway de tablas del print-path + params Todos; no omitir rama como camino primario). D-TUR-62.

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md). Misma que T5.5.

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto) |
| Ramas `esClienteX()` | ninguna en `BBConsultaAgenda` / print-path `ConsultaAgenda` |
| Decisión | sin ramas por instalación · resto de clientes `diferido(multi-instalacion)` |

## Problema

T5.5 cableó Imprimir → sidecar `ConsultaAgenda`. G6 visual 2026-09-14: grilla **con filas**, PDF **con columnas sin valores**.

Causa (2026-09-15): primero el package reventaba por `ts.turno_vencido` ausente (V56). Después el SELECT corría y volvía 0 filas: default BIRT `44983` + Api `""` → BIRT float `0`.

DoR del cable (no re-portar diseño): [`regla-migracion-reportes-birt.md`](../../../canon/regla-migracion-reportes-birt.md) § Restricciones punto 5.

## Resultado (objetivo Camino 1)

Consultar **hoy** → Imprimir → PDF con **los mismos valores** que la grilla JDBC (`ts.turno`). Equipo puede ir vacío (D-TUR-17). Histórico (fecha &lt; hoy) vacío hasta T6 job. Excel sigue en [`turnos-agenda-consultas-export/`](../turnos-agenda-consultas-export/).

## Clarify — **FIRME Camino 1** (2026-09-15)

Firma producto: Francisco — análisis tablas faltantes; «no está mal agregarlas a Flyway»; DoR cable ≠ port Reports; lista de trabajo DDL + params; «habría que hacer clarify».

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T5.5 **gate-done** (cable Imprimir). Reports `ConsultaAgenda` portado. | verify T5.5 · CHECKLIST Reports |
| 2 | ¿Misma ruta / diseño? | **Sí** GET ya cableado. **No** re-migrar `.rptdesign`. **No** embeber BIRT en Api. | T5.5 RF-9 |
| 3 | ¿Happy path? | Vista consulta → Consultar hoy → Imprimir → PDF con paciente/centro/servicio/personal/prestación/estado/hora = grilla. Filtro **Todos** (sin ids) y con centro. | `actBtnImprimir` · dump 2 filas `ts.turno` hoy |
| 4 | ¿Ciclo de vida? | **No** job `f_migra_turno_vencido`. Tabla `turno_vencido` **vacía** desbloquea el UNION. Hoy = `ts.turno`. | T6 `turnos-ciclo-vida` |
| 5 | ¿Errores? | Package que no corre → PDF cabeceras vacías = **FAIL** (no PASS). Tras DDL: `to_regclass` + `SELECT count(*) FROM turnos.p_get_turnos_fecha(...)` = count grilla hoy. | dump 2026-09-15 ERROR `turno_vencido` |
| 6 | ¿Side-effects / DDL? | Flyway **V56** `ts.turno_vencido` IF NOT EXISTS (forma `ts.turno`, PK `id_turno_vencido`). Flyway **V56** `ts.equipo_serv_centro` IF NOT EXISTS **vacía**. Package: `NULLIF(id, 0)`. Api: omitir ids Todos (no `""`). Sidecar: sin default BIRT `44983`. Reinstall package en piloto. | `p_get_turnos_fecha.sql` · handler `putOptionalId` · `ConsultaAgenda.rptdesign` |
| 7 | ¿Paridad UI / Gate? | **N/A** pantalla nueva. South Imprimir ya T5.5. No template. | Gate UI padre cerrado |
| 8 | ¿Viaje Playwright? | **N/A** (motivo: no hay pantalla nueva; e2e T5.5 ya stub blob CI). G6 visual **sí**. | [`regla-playwright-migracion.md`](../../../canon/regla-playwright-migracion.md) |
| 9 | ¿Fuera? | Excel. Job vencidos / hist. Equipo usable D-TUR-17. Re-port diseño. Rama omitida como plan A. `tmp_turno` / LIBRE. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Solo omitir `turno_vencido` si no existe (T4) | Francisco: registrar tablas en Flyway como el resto de cortes. La omit-rama queda de reserva si el dump remoto **ya** tiene otra forma. |
| DDL + job `f_migra_turno_vencido` | T6. No este hijo. |
| Solo params BIRT (`44983`) | El package revienta **antes** de filtrar. |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Imprimir PDF con valores = grilla hoy | `BBConsultaAgenda.actBtnImprimir` | **In scope** | No (DDL vacío sí) |
| Cable GET sidecar | T5.5 | **hecho** | No |
| Print-path `p_get_turnos_fecha` corre en piloto | package Reports | **In scope** (DDL + params + reinstall) | No |
| Histórico fecha &lt; hoy | `turno_vencido` poblado | **diferido** T6 | — |
| Columna equipo con catálogo | `equipo_serv_centro` | **diferido** D-TUR-17 (tabla vacía solo para no crashear) | No |
| Excel POI | `generarReporteExcel` | **diferido** export | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | `ts.turno_vencido` en Flyway, forma Oracle TS, `IF NOT EXISTS`, 0 filas. |
| RF-2 | `ts.equipo_serv_centro` en Flyway, `IF NOT EXISTS`, 0 filas (no ABM equipo). |
| RF-3 | `p_get_turnos_fecha` corre en el dump; `NULLIF` ids `0`; reinstall piloto. |
| RF-4 | Api Todos: ids ausentes ≠ `""` (null / omitir). Quitar default `44983` del diseño sidecar. |
| RF-5 | G6: PDF hoy con valores = grilla. Equipo puede ir vacío. |
| NFR-1 | No re-portar `.rptdesign` (salvo default `44983` + bindings ya minúsculas). No BIRT en Api. |
| NFR-2 | Un writer Flyway. Dump remoto: `IF NOT EXISTS`. |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-62 | Camino 1 hijo PDF: Flyway `turno_vencido` + `equipo_serv_centro` vacías; params Todos; no job T6; no re-port diseño; G6 visual hoy = grilla. Hueco Flyway = corte que cablea ([regla BIRT](../../../canon/regla-migracion-reportes-birt.md) §5). |

## No objetivos

| Ítem | Destino |
|------|---------|
| Excel | `turnos-agenda-consultas-export` |
| Job / hist vencidos | `turnos-ciclo-vida` (T6) |
| Equipo usable | D-TUR-17 |
| Re-migrar layout BIRT | **N/A** — ya en Hospital-Reports |
| `tmp_turno` / cupos LIBRE | diferido CHECKLIST Reports |

## Evidencia (paths)

```
Hospital-Reports/sql/packages-pg/turnos/p_get_turnos_fecha.sql  UNION ts.turno_vencido; NULLIF id 0
Hospital-Reports/src/main/resources/designs/ConsultaAgenda.rptdesign  sin default idCentroAte
Hospital-Api/.../GetConsultaAgendaPdfQueryHandler.java  putOptionalId (omite Todos)
Hospital-Api/.../db/migration/V56__ts_turno_vencido_equipo_serv_centro.sql
```
