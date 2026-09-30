---
title: Spec — T6.2 · cola Reasignación de Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar
---

# Spec — Cola Reasignación de Turnos

Padre T6 `turnos-ciclo-vida` (resto **diferido**).  
Hermano: [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) T6.1 **gate-done**.  
Ruta: **`/turnos/agenda`**. **NO fork.** Accordion **Reasignación de Turnos** live.  
Clarify **FIRME Camino 1** — 2026-09-16. D-TUR-64. Francisco: «ok».

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md). Misma que T5/T6.1.

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto) |
| Ramas `esClienteX()` | ninguna en `BBTurnosAReasignar` |
| Decisión | sin ramas por instalación · resto `diferido(multi-instalacion)` |

## Problema

T6.1 cobró el engranaje de **fila** en Agenda. El accordion **Reasignación de Turnos** sigue disabled con tooltip `diferido(turnos-ciclo-vida)`. El HIS lista `ts.turno_a_reasignar` (T4 eliminar ya inserta) y el gear **REASIGNAR TURNO** salta a Agenda con sesión `TurnoAReasignar`.

## Resultado (objetivo Camino 1)

Misma ruta. Ítem accordion enabled. North filtros (centro/servicio/profesional/fechas/horas; **equipo disabled** D-TUR-17). Consultar lista la cola. Vacío = `no_se_encontraron_registros`. Gear **REASIGNAR TURNO** → Agenda con north hidratado y sesión `turnoAReasignar=true` (HIS **no** pinta overlay `PENDIENTE_LIBERAR` — eso es el flujo de fila T6.1). Gear **INFORMACIÓN** → popup info paciente (reusa T5.1). Botón observaciones → popup 700px → UPDATE `ts.turno_a_reasignar.observaciones`. Otorgar destino **DELETE** fila cola (no libera origen: no hay `id_turno` origen). South Imprimir y Exportar Excel **diferidos** (hijos).

## Clarify — **FIRME Camino 1** (2026-09-16)

Firma producto: Francisco pidió arrancar Clarify de accordion Reasignar / Historial / Avisos. Este corte = **solo cola**.

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T4 **gate-done** (INSERT cola). T5/T6.1 **gate-done**. Call center T5. | verify padres |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. **NO fork.** Habilitar accordion `reasignacion`. | `asignacionTurnos.xhtml` L82–94 · `turnosAReasignar.xhtml` |
| 3 | ¿Happy path? | Abrir Reasignación de Turnos → default fechas hoy / horas 00:00–23:59 → Consultar → tabla `Turnos A Reasignar` → gear **REASIGNAR TURNO** → Agenda con paciente/convenio/prestación/centro/servicio/personal de la fila + sesión `TurnoAReasignar` (`turnoAReasignar=true`). Cierre = T6.1. | `actBtnConsultar` ~197 · `actBtnIrAGrilla` ~286 · `BBAgenda` ~861 (`!isTurnoAReasignar()` = overlay solo flujo fila) |
| 4 | ¿Ciclo de vida? | Lista = SELECT. Obs = UPDATE `ts.turno_a_reasignar`. IrAGrilla **no** escribe. DELETE cola al cerrar reasignación = T6.1/T5 (fuera si ya está). | Hibernate `updateObservacionesTurnoAReasignar` · PK = `id_turno_a_reasignar` expuesto como `idTurno` (rareza HIS) |
| 5 | ¿Errores / permisos? | `fechaHasta` posterior a `fechaDesde` → `WRONG_INTERVAL_DATE_3`. Horas → `WRONG_INTERVAL_HOUR`. Vacío: mensaje tabla, **no** toast. Obs OK → `OBSERVACION_AGREGADA_EXITO`. Call center sesión T5. 401/403 sin `/500`. Actor **sin** perfil de Agenda: no entra (menú). Bean **sin** `Raise_application_error` de rol funcional. | MessageBundle · BB L199–204 · L383 |
| 6 | ¿Side-effects? | JDBC list equivalente a `f_get_turnos_a_reasignar` (**rediseñar**, sin GTT `tmp_turno`, sin clonar BODY). UPDATE obs JDBC. **No** Flyway. Jobs: **N/A** (la cola la carga T4, no un scheduler). Imprimir/Excel south → hijos. | `fTurnosAReasignar` · V43 |
| 7 | ¿Paridad UI / Gate? | **No** xhtml nuevo. Misma ruta; vista accordion. Inventarios G0 **antes** de template. North labels 111px. South botones 150px **visibles disabled** + tooltip hijo (como T5.5 Excel antes del hijo). Equipo combo disabled. **Delta D-TUR-66 (2026-09-17):** Consultar y Limpiar Datos en fila propia centrada (como Agenda); HIS los deja icon-only inline (L108–115). **Delta D-TUR-67 (2026-09-17):** col Acciones al final; gear + observaciones visibles (HIS first `width=60` + gear 100% recorta el segundo). | `turnosAReasignar.xhtml` · Gate UI arranque · D-TUR-66 · D-TUR-67 |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) accordion enabled; (b) Consultar vacío muestra copy HIS; (c) con fixture T4 → filas; (d) REASIGNAR TURNO llega a Agenda con sesión. Legacy HIS **no**. | `regla-playwright-migracion.md` |
| 9 | ¿Fuera? | T6.1 fila. Suspender. Reemplazo. Historial. Avisos (`disabled=true` → **WAIVE**). Equipo D-TUR-17. Imprimir. Excel. HOS-APP. T7. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Fork `/turnos/agenda/cola` | HIS es include del mismo chrome Agenda |
| Abrir T6 padre (suspender+reemplazo+hist+cola) | Techo; T6.1 ya partió el padre |
| Portar `f_get_turnos_a_reasignar` a TURNOS BODY | GTT + loop día; T5.5 ya eligió JDBC equivalente. **rediseñar** |
| Reimplementar overlay `PENDIENTE_LIBERAR` desde cola | HIS **no** lo pinta si `turnoAReasignar=true` |

**HIS in-memory vs re-query.** Consultar carga `listTurnos`. Camino 1: GET con los filtros del north (como T5.5 `lastFiltro` al Consultar).

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Accordion Reasignación de Turnos live | `asignacionTurnos.xhtml` ítem live | **In scope** | No |
| Consultar cola | `actBtnConsultar` · `f_get_turnos_a_reasignar` | **In scope** | No |
| Limpiar north | `actBtnLimpiarDatos` | **In scope** | No |
| Gear REASIGNAR TURNO → Agenda | `actBtnIrAGrilla` | **In scope** (sesión; cierre T6.1) | No |
| Gear INFORMACIÓN | `actionBtnReasignarTurno` → popup paciente | **In scope** (reusa T5.1) | No |
| Popup observaciones cola | `$popUpObservacionesTurnoAReasignar` 700px | **In scope** | **Sí** UPDATE obs |
| Imprimir south | `actBtnImprimir` | **done** hijo [`turnos-agenda-cola-reasignar-print/`](../turnos-agenda-cola-reasignar-print/) **gate-done** | No |
| Exportar Excel south | `actionBtnExportarExcel` | **hijo gate-done** [`turnos-agenda-cola-reasignar-export/`](../turnos-agenda-cola-reasignar-export/) | No |
| Equipo usable | combo north | **diferido** D-TUR-17 | — |
| Historial accordion | `BBHistorialTurno` | **diferido** `turnos-ciclo-vida` | — |
| Avisos accordion | `disabled=true` | **WAIVE** (HIS apagado) | — |
| Suspender / reemplazo | beans T6 | **diferido** `turnos-ciclo-vida` | — |
| T6.1 fila Agenda | `actBtnReemplazarTurno` | **fuera** (gate-done) | — |
| Jobs programados | ningún job escribe/lee esta cola | **N/A** (T4 es el alta; `MigrarTurnoVencidoJob` es otro circuito) | — |
| HOS-APP cola | `turnosonline/turnosAReasignar.xhtml` | **N/A** (otro producto) | — |

## Firmas PL/SQL

| Firma | Decisión | Nota |
|-------|----------|------|
| `TS.TURNOS.f_get_turnos_a_reasignar` | **rediseñar** JDBC sobre `ts.turno_a_reasignar` | Equivalente filtros (centro/servicio/personal/equipo/fechas+horas/call center). Sin GTT. `id_turno` de la fila = PK cola (rareza). Estado UI «A REASIGNAR» si el cursor lo pinta. |
| `p_get_turnos_a_reasignar` (print-path) | **fuera** (hijo Imprimir) | No citar motor en verify |
| UPDATE observaciones | **rediseñar** JDBC | HIS Hibernate `TurnoAReasignar`; no hay función PL/SQL |

## Acceso

Mismo perfil de menú que Agenda (`ATENCION_TURNOS` / tile Turnos). Rol funcional: **ninguno** en este bean (no `Raise_application_error`). Prueba negativa: actor **sin** entrada de menú Agenda — no abre `/turnos/agenda`. PASS con administrador no cuenta.

Trazabilidad: si `turno_a_reasignar` está en el catálogo `TBL_AUD_*` y el UPDATE obs no audita campo a campo → `diferido(auditoria)` al implementar (no silencio). `fecha_last_update` / `actualizado_por` = último cambio, no historial.

## Presupuesto no funcional

| Eje | Objetivo | Cómo se mide |
|-----|----------|----------------|
| Tiempo | p95 GET cola ≤ 2 s en piloto (percentil, no promedio) | reloj G6 / IT |
| Volumen | orden del dump `ts.turno_a_reasignar`; si COUNT=0 al FIRME: «medido en vacío» + **no PASS** hasta T4 elimine o `diferido(fixture)` | COUNT + filas UI |
| Concurrencia | HIS **no** `FOR UPDATE` en list ni en UPDATE obs (last-write-wins). Dos actores: (a) consultar misma cola; (b) dos UPDATE obs misma PK — gana el último, como Hibernate. | IT dos sesiones. **No** «no aplica»: hay escritura. Rollback del PATCH si falla. |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Accordion Reasignación de Turnos enabled; Avisos/Historial siguen según su decisión. |
| RF-2 | North: centro, servicio, profesional+lupa, fechas, horas, Consultar, Limpiar. Equipo visible disabled. Defaults: hoy / 00:00–23:59. |
| RF-3 | Consultar: lista columnas HIS (fecha 64, hora 52, duración 52, paciente, centro, servicio, profesional/equipo, prestación, convenio, plan, teléfono, **acciones al final** D-TUR-67). Header `Turnos A Reasignar`. |
| RF-4 | 0 filas: `No se encontraron registros` (copy HIS). Sin toast. |
| RF-5 | Fechas/horas inválidas: toasts HIS `_3` / `WRONG_INTERVAL_HOUR`. |
| RF-6 | REASIGNAR TURNO: navega a hoja Agenda con sesión T6.1 y `turnoAReasignar=true` (sin overlay pendiente). |
| RF-7 | INFORMACIÓN: popup info paciente (reusa T5.1). |
| RF-8 | Observaciones: dialog 700px, `closable=false`, Aceptar persiste y toast éxito; Cancelar cierra. |
| RF-9 | South Imprimir/Excel 150px **disabled** + tooltip hijo. Leyenda south copy HIS. |
| RF-10 | e2e-migrado 4 viajes. G6: ops ve filas de T4 (no seed). |
| NFR-1 | Resource delgado. JDBC en infrastructure. Sin SQL en Resource. |
| NFR-2 | Sin Flyway. Sin fork. Sin clonar TURNOS BODY. |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-64 | Camino 1 cola accordion: misma `/turnos/agenda`; JDBC list+UPDATE obs; IrAGrilla reusa T6.1; Imprimir/Excel hijos; Equipo D-TUR-17; Historial T6 padre; Avisos WAIVE (`disabled=true`); no reabrir T6.1. |
| D-TUR-66 | Firma producto 2026-09-17 (Francisco): Consultar y Limpiar Datos de la cola en fila propia centrada, iguales a Agenda. No volver a icon-only inline HIS (L108–115). |
| D-TUR-67 | Firma producto 2026-09-17 (Francisco): col Acciones al final de la tabla; gear (REASIGNAR TURNO / INFORMACIÓN) y observaciones visibles juntos. HIS las deja first `width=60` con gear 100%. |

**Invariantes:** misma ruta; no seed de cola; no overlay desde cola; PK cola ≠ id de `ts.turno`.  
**Contingentes:** alcance INFORMACIÓN si el popup T5.1 no cubre; COUNT=0 → `diferido(fixture)` vs ops corre T4.

## No objetivos

| Ítem | Destino |
|------|---------|
| Reasignar fila Agenda | T6.1 (hecho) |
| Imprimir cola | [`turnos-agenda-cola-reasignar-print/`](../turnos-agenda-cola-reasignar-print/) |
| Excel cola | [`turnos-agenda-cola-reasignar-export/`](../turnos-agenda-cola-reasignar-export/) |
| Equipo usable | D-TUR-17 |
| Historial / suspender / reemplazo / vencidos | `turnos-ciclo-vida` |
| Avisos | **WAIVE** (`disabled=true`) |
| HOS-APP | N/A |

## Universo (techo)

Semilla: `turnosAReasignar` (xhtml). 1 hop: buscador personal (ya T5). Tronco `asignacionTurnos.xhtml` / `BBAsignacionTurnos` info paciente = **reuso**, no portar.  
Cota: 1 xhtml · 1 bean propio · 0 firma BODY nueva · 0 diseño de impresión en este slug. **No EXCEDE.**

## Evidencia (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/turnosAReasignar.xhtml
Hospital-Legacy/HOSPITAL_2/src/.../BBTurnosAReasignar.java actBtnConsultar · actBtnIrAGrilla · update obs
Hospital-Legacy/HOSPITAL-BUSINESS/.../Turnos.hbm.xml fTurnosAReasignar
Packages/Turnos/TURNOS.PACKAGE_BODY.sql f_get_turnos_a_reasignar ~11841
```
