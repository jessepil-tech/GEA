---
title: Spec — T6.3 · Historial Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial
---

# Spec — Historial Turnos

Padre T6 `turnos-ciclo-vida` (resto **diferido**).  
Ruta: **`/turnos/agenda`**. **NO fork.** Accordion **Historial Turnos** live.  
Clarify **FIRME Camino 1** — 2026-09-18. D-TUR-70. Francisco: «dale arranquemos».

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md). Misma que T5/T6.2.

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto) |
| Ramas `esClienteX()` | ninguna en `BBHistorialTurno` |
| Decisión | sin ramas por instalación · resto `diferido(multi-instalacion)` |

## Problema

T6.2 cobró la hoja accordion **Reasignación**. **Historial Turnos** sigue disabled con tooltip `diferido(turnos-ciclo-vida)`. El HIS lista `ts.hist_turno` (la llena T5 otorga/libera) y el ícono info abre `$popupInfoHistTurno`.

## Resultado (objetivo Camino 1)

Misma ruta. Ítem accordion enabled. North filtros (centro **111px** D-TUR-71 / servicio 70px / profesional+lupa / **equipo disabled** D-TUR-17 / fechas hoy–hoy / horas 00:00–23:59 / Consultar con copy **fila centrada** D-TUR-71). Consultar lista hist. Vacío = `no_se_encontraron_registros`. Ícono **Información Turno** → dialog 1200×512 read-only. South leyenda 4 estados. South **Exportar Excel** visible **disabled** + tooltip hijo.

## Clarify — **FIRME Camino 1** (2026-09-18)

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T5 **gate-done** (INSERT/UPDATE `hist_turno` al otorga/libera). T6.2 accordion chrome. Call center T5. | verify padres |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. **NO fork.** Habilitar accordion `historial`. | `asignacionTurnos.xhtml` L92–94 · `historialTurno.xhtml` |
| 3 | ¿Happy path? | Abrir Historial Turnos → default fechas hoy / horas 00:00–23:59 → Consultar → tabla `Historial Turnos` → info → dialog datos turno/paciente/prep/req/docs/próximos. | `actBtnConsultar` L159 · `infoHistTurno` L232 |
| 4 | ¿Ciclo de vida? | Lista = SELECT. Info **no** escribe. Alta hist = T5 (fuera). | `ImpHistTurno.selectHistTurnoEntreFechas` · Criteria `between fechaHoraTurIni` |
| 5 | ¿Errores / permisos? | Horas invertidas → `WRONG_INTERVAL_HOUR`. **No** valida intervalo de fechas (HIS no tira `_3`). Vacío: mensaje tabla, **no** toast. Call center: combos sí; **query hist no filtra call center**. Actor **sin** perfil Agenda: no entra. Bean **sin** `Raise_application_error`. | BB L162–171 · MessageBundle |
| 6 | ¿Side-effects? | JDBC list equivalente a Hibernate Criteria (**rediseñar**). Teléfono = JOIN `te_persona` (no portar `personas.f_get_persona_telefono`). Excel → hijo. Jobs: **N/A** esta hoja (alta = T5; `MigrarTurnoVencidoJob` = padre vencidos). | ImpHistTurno · hbm formulas |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Labels centro **111px** (95 HIS recorta copy; mismo ancho Consulta/Cola) / servicio `70px`. Consultar con copy **fila centrada** D-TUR-71 (HIS inline; igual Agenda/Consulta). Col info first `width=24`. Popup 1200×512 `closable=true`. Equipo disabled. Excel south 150px disabled. | `historialTurno.xhtml` · Gate UI arranque · Francisco 2026-09-18 |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) accordion enabled; (b) Consultar vacío muestra copy HIS; (c) con fixture T5 → filas; (d) info abre dialog. Legacy HIS **no**. | `regla-playwright-migracion.md` |
| 9 | ¿Fuera? | Excel. Equipo D-TUR-17. Suspender / reemplazo / vencidos. Avisos WAIVE. Menú `consultaHistorialTurno`. HOS-APP. T7 tickets. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Fork `/turnos/agenda/historial` | HIS es include del mismo chrome Agenda |
| Abrir T6 padre entero | Techo; T6.1/T6.2 ya partieron el padre |
| Portar Criteria a TURNOS BODY | No hay `f_get_hist_*`; **rediseñar** JDBC |
| Filtrar hist por call center | HIS **no** lo pasa al SELECT (solo a combos) |
| Inventar `WRONG_INTERVAL_DATE_3` | El bean **no** lo tira |
| Reusar dialog T5.1e infoTurno tal cual | HIS tiene `$popupInfoHistTurno` propio (1200×512, hist snapshot) |

**HIS in-memory vs re-query.** Consultar carga `listTurnos`. Camino 1: GET con los filtros del north (como T5.5/T6.2). Constructor **no** consulta.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Accordion Historial Turnos live | `asignacionTurnos.xhtml` L92–94 | **In scope** | No |
| Consultar hist | `actBtnConsultar` · Criteria `hist_turno` | **In scope** | No |
| Combos centro/servicio (call center) | `initSelectItem*` T5 | **In scope** (reuso) | No |
| Profesional + lupa | `buscarPersonal` buscador T5 | **In scope** (reuso) | No |
| Equipo usable | combo north live HIS | **diferido** D-TUR-17 | — |
| Ícono Información Turno | `infoHistTurno` · popup 1200×512 | **In scope** | No |
| Prep / req / docs / ficha en popup | Configuracion/Convenios/Pacientes | **In scope** (reuso T5.1e-q / ficha) | No |
| Próximos turnos en popup | `selectTurnosPorPaciente` | **In scope** | No |
| Leyenda south 4 estados | `referenciasTurnos` | **In scope** | No |
| Exportar Excel south | `actBtnExportarExcel` · XLSParser | **done** hijo [`turnos-agenda-historial-export/`](../turnos-agenda-historial-export/) | No |
| Estilo fila (sobreturno / cancelado / reasignado / reemplazado) | `HistTurno.getEstilo` | **In scope** | No |
| Label `SUSPENDIDO` → `CANCELADO` | `getEstadoTurnoLabel` | **In scope** (rareza) | No |
| Avisos accordion | `disabled=true` | **WAIVE** | — |
| Suspender / reemplazo / vencidos | beans T6 | **diferido** `turnos-ciclo-vida` | — |
| Menú Consulta Historial Turno | `BBConsultaHistorialTurno` | **diferido** [`turnos-consultas-operador/`](../turnos-consultas-operador/) | — |
| `MigrarTurnoVencidoJob` | scheduler | **N/A** (padre vencidos; no esta hoja) | — |
| `MailTurnoJob` | scheduler | **N/A** T7 | — |
| HOS-APP hist | `turnosonline/historialTurno.xhtml` | **N/A** (otro producto) | — |

## Firmas PL/SQL

| Firma | Decisión | Nota |
|-------|----------|------|
| Hibernate `selectHistTurnoEntreFechas` | **rediseñar** JDBC `ts.hist_turno` | `BETWEEN fecha_hora_tur_ini` (fecha+hora concatenados). Filtros opcionales centro/servicio/personal/equipo. **Sin** call center en el WHERE. |
| `ts.personas.f_get_persona_telefono` (formula hbm) | **rediseñar** JOIN `ts.te_persona` | Igual T6.2; no puente Oracle |
| Formulas `decode` nombre persona | **rediseñar** `||` + `CASE` PG | Oracle `decode` no se porta literal |
| Alta `hist_turno` | **fuera** (T5 ya escribe) | No reabrir otorga/libera |

## Acceso

Mismo perfil de menú que Agenda (`ATENCION_TURNOS` / tile Turnos). Rol funcional: **ninguno** en este bean. Prueba negativa: actor **sin** entrada de menú Agenda — no abre `/turnos/agenda`. PASS con administrador no cuenta.

Trazabilidad: este corte **no escribe**. Auditoría **N/A**. `fecha_modifica` / `id_personal_modifica` de la fila hist = último cambio HIS, se **muestran**, no se mutan.

## Presupuesto no funcional

| Eje | Objetivo | Cómo se mide |
|-----|----------|----------------|
| Tiempo | p95 GET hist ≤ 2 s en piloto (percentil, no promedio) | reloj G6 / IT |
| Volumen | orden del dump `ts.hist_turno` filtrado hoy; si COUNT=0 al FIRME: «medido en vacío» + **no PASS** hasta T5 otorga/libera o `diferido(fixture)` | COUNT + filas UI |
| Concurrencia | HIS **no** `FOR UPDATE` (solo SELECT). Recurso no disputado; dos actores en UI **N/A**. | JDBC `queryHistTurno`; no cupo/cama/stock |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Accordion Historial Turnos enabled; Avisos sigue WAIVE. |
| RF-2 | North: centro, servicio, profesional+lupa, fechas hoy/hoy, horas 00:00–23:59, Consultar con copy. Equipo visible disabled. |
| RF-3 | Consultar: lista columnas HIS (info 24, fecha 50, hora 50, duración 50, estado 66, paciente 120, teléfono 90, centro 120, servicio 120, profesional/equipo 120, cod prest 60, prestación 120, fecha mod 130, usuario mod 120, tipo sol 80, tipo canc 80). Header `Historial Turnos`. |
| RF-4 | 0 filas: `No se encontraron registros`. Sin toast. |
| RF-5 | Horas inválidas: toast HIS `WRONG_INTERVAL_HOUR`. Fechas invertidas: HIS no toasta (between vacío o raro) — **no** inventar `_3`. |
| RF-6 | Estilo fila + label estado paridad `getEstilo` / `getEstadoTurnoLabel`. |
| RF-7 | Info: dialog 1200×512, `closable=true`, Volver cierra. Datos read-only. |
| RF-8 | South leyenda 4 textos HIS. Excel 150px **disabled** + tooltip hijo. |
| RF-9 | e2e-migrado 4 viajes. G6: ops ve hist de T5 (no seed). |
| NFR-1 | Resource delgado. JDBC en infrastructure. Sin SQL en Resource. |
| NFR-2 | Sin Flyway. Sin fork. Sin clonar TURNOS BODY. |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-70 | Camino 1 hoja accordion Historial: misma `/turnos/agenda`; JDBC list+info; Excel hijo; Equipo D-TUR-17; query **sin** call center; no `WRONG_INTERVAL_DATE_3`; rareza `SUSPENDIDO`→`CANCELADO`; no reabrir T5 hist write; no T6 padre entero. |

**Invariantes:** misma ruta; no seed de hist; no filtrar call center en el SELECT; no toast de fechas.  
**Contingentes:** COUNT=0 → G6 T5 otorga/libera vs `diferido(fixture)`; alcance popup si T5.1e-q no cubre un panel.

## No objetivos

| Ítem | Destino |
|------|---------|
| Excel hist | [`turnos-agenda-historial-export/`](../turnos-agenda-historial-export/) |
| Equipo usable | D-TUR-17 |
| Suspender / reemplazo / vencidos | `turnos-ciclo-vida` |
| Avisos | **WAIVE** (`disabled=true`) |
| Consulta Historial menú | [`turnos-consultas-operador/`](../turnos-consultas-operador/) |
| HOS-APP | N/A |
| Tickets / mail | T7 |

## Universo (techo)

Semilla: `HOSPITAL_2/.../historialTurno.xhtml` (no el basename ambiguo: HOS-APP).  
1 hop: `asignacionTurnos.xhtml` chrome (reuso, no portar). Buscador personal T5 (reuso).  
Candidatos índice: 2 xhtml · 5 beans · 0 firmas `f_/p_` · 3 diseños del chrome padre → **fuera** (T7 / infoTurno).  
Cota firmada: 1 xhtml propio · 1 bean propio · 0 BODY · 0 diseño de impresión. **No EXCEDE.**

Jobs: `hist_turno` / `historial` → 0. `vencido` → `MigrarTurnoVencidoJob` **N/A** esta hoja. `MailTurnoJob` **N/A** T7.

## Evidencia (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/historialTurno.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/asignacionTurnos.xhtml L92–94
Hospital-Legacy/HOSPITAL_2/src/.../BBHistorialTurno.java actBtnConsultar · infoHistTurno · actBtnExportarExcel
Hospital-Legacy/HOSPITAL-BUSINESS/.../ImpHistTurno.java selectHistTurnoEntreFechas
Hospital-Legacy/HOSPITAL-BUSINESS/.../HistTurno.java getEstilo · getEstadoTurnoLabel · getPersonalEquipo
Hospital-Legacy/HOSPITAL-BUSINESS/.../HistTurno.hbm.xml formulas
```
