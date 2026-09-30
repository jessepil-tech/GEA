---
title: Spec — T6 · migrar turno vencido
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-ciclo-vida
---

# Spec — Migrar turno vencido

Padre T6 origin (2026-08-27): suspender / reemplazo / reasignar / vencidos / hist.  
Hijos T6.1–T6.5 **gate-done**. Queda el job.  
Clarify **FIRME Camino 1** — 2026-09-21 (Francisco). D-TUR-75.

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md).

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto, `cliente="TS"`) |
| Ramas `esClienteX()` | ninguna en `f_migra_turno_vencido` |
| Decisión | camino genérico · resto `diferido(multi-instalacion)` |

## Problema

La oferta de días anteriores sigue en `ts.turno`. Consulta con fecha `< trunc(hoy)` lee `turno_vencido` (UNION T5.5-pdf). Sin el job, esa tabla queda vacía y la cola de recepción con `llamado='N'` no se limpia a las 6 h. El WAR `SCHEDULER` está **fuera de alcance**; la capacidad no.

## Resultado (objetivo Camino 1)

Un comando en Hospital-Api, paridad de `TS.TURNOS.f_migra_turno_vencido` **acotada**:

1. Purga cola recepción y triage: `llamado='N'` y `fecha_hora_ingreso_cola < now()-6h` → `'S'`.
2. No reescribir el safety 1 h de `llamado_anunciador` (ya corre `LlamadoPacienteExpireScheduler` cada 15 min).
3. Cursor `FOR UPDATE` de `ts.turno` con `fecha_hora_tur_ini < trunc(hoy)`: copiar a `turno_vencido` (mismo id), copiar `mensaje_turno` vigente a `mensaje_turno_vencido`, desenganchar FKs, borrar `mensaje_turno` y `turno`.
4. Disparador **fino** en Api: `@Scheduled` + POST de prueba (paridad `Job.main()`). No clonar Quartz/`tarea_programada`.

Fuera de este slice: armar mail/SMS **nuevo** `REPROGRAMACION` (`genera_mails.*`) → T7; marcar enviados a 7 días → T7; `mensaje_kern` → N/A; `alter system disconnect` → N/A.

## Clarify — **FIRME Camino 1** (2026-09-21)

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T4/T5 **gate-done**. DDL `turno_vencido` ya existe (T5.5-pdf, vacía). | V56 · A8 |
| 2 | ¿Misma familia UI? | **No hay hoja.** Semilla = `MigrarTurnoVencidoJob`. Gate UI **N/A**. | `--jobs turno` |
| 3 | ¿Happy path? | Correr el comando → filas con inicio **ayer** desaparecen de `turno` y nacen en `turno_vencido` con el mismo id; mensajes pendientes viajan; cola >6 h queda `llamado='S'`. | BODY 9567–9776 |
| 4 | ¿Ciclo de vida? | Criterio = calendario (`trunc(sysdate)`), no el `estado_turno`. LIBRE y OTORGADO se archivan igual. `hist_turno` **no** se toca en este SP. | cursor `c_vencido` |
| 5 | ¿Errores / permisos? | SP **sin** `rol_funcional_pers` (no aborta por perfil). HIS corre con conexión de interfaz (`openInterfaceConexion(2)`). Destino: JWT (POST) o job interno; actor **sin** token → 401. Flag apagado → no-op. PASS admin no cuenta. | Job.java · regla acceso |
| 6 | ¿Side-effects? | **Portar** archivo + purga 6 h + unlinks. **Reusar** expire 1 h ya cobrado. `MailTurnoJob` **T7**. `CheckHabTurnosJob` **N/A** (T2). `genera_mails.f_mail_reprogramacion_turno` / `f_sms_reprogramacion_turno` **T7**. Sin sidecar. | BODY 9621–9773 · 9808–9868 |
| 7 | ¿Paridad UI / Gate? | **N/A** — no hay acto de usuario ni hoja a medir. | — |
| 8 | ¿Viaje Playwright? | **N/A** (motivo: proceso sin acto de usuario en Web). Evidencia = golden + IT + G6 SQL. | `regla-playwright-migracion.md` |
| 9 | ¿Fuera? | T7 avisos (alta **nueva** REPROGRAMACION + despacho + housekeeping 7 d). ABM `tarea_programada` / equipo. `CheckHab`. Kern. Disconnect Oracle. T6.1–T6.5. | slugs abajo |

**Caminos**

| Camino | Qué entra | Por qué |
|--------|-----------|---------|
| **1 (propuesto)** | Archivo + cola 6 h + disparador Api; reusar expire 1 h; copiar mensajes **ya existentes** | Techo: `genera_mails` es otro package; T7 ya es el despacho; SCHEDULER fuera ≠ sin capacidad |
| 2 | SP entero salvo disconnect | 1:1 del BODY; infla con plantillas mail/SMS y Kern |
| Descartado: solo archivo, sin cola 6 h | — | El overnight del mostrador queda sucio (mismo SP, primer `UPDATE`) |
| Descartado: clonar WAR SCHEDULER | — | Fuera de alcance del cliente; lógica vive en TURNOS + HOSPITAL-BUSINESS |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Archivar oferta < hoy | `f_migra_turno_vencido` cursor | **In scope** Camino 1 | **Sí** |
| Copiar `mensaje_turno` vigente | INSERT SELECT + DELETE | **In scope** | **Sí** |
| Unlinks internación / SMS / cola amb | UPDATE id a null | **In scope** | **Sí** |
| Purga cola recepción/triage 6 h | primer UPDATE del SP | **In scope** Camino 1 | **Sí** |
| Apagar TV 1 h | `llamado_anunciador` | **N/A** — ya cobrado (expire 15 min) | — |
| Alta REPROGRAMACION | `genera_mails.*` si OTORGADO | **diferido** T7 | — |
| Despacho mail/SMS | `MailTurnoJob` | **diferido** T7 | — |
| Housekeeping 7 d / server null | UPDATE enviado='S' | **diferido** T7 | — |
| `mensaje_kern` | hist 90 d / 1 d | **N/A** este circuito | — |
| `alter system disconnect` | PACIENTE_TS / ENFERMERA_TS | **N/A** (parche Oracle; no se porta) | — |
| Vigencia hab | `CheckHabTurnosJob` | **N/A** T2 | — |
| ABM `tarea_programada` | menú 10294 | **diferido(disparador)** | — |

## Firmas PL/SQL

| Firma | Decisión | Nota |
|-------|----------|------|
| `TURNOS.f_migra_turno_vencido` | **portar** (golden master, recorte Camino 1) | FOR UPDATE obligatorio |
| `GENERAL.f_next_id_tabla` | **N/A** Camino 1 | solo si entra alta REPROGRAMACION (T7); el archivo reusa `id_turno` |
| `GENERA_MAILS.f_mail_reprogramacion_turno` | **diferido** T7 | Camino 2 la traería |
| `GENERA_MAILS.f_sms_reprogramacion_turno` | **diferido** T7 | Camino 2 |

Casos golden mínimos: vacío (nada que archivar), LIBRE ayer, OTORGADO ayer, fecha **= hoy** (no archiva), cero filas, NULL paciente, borde `trunc(hoy)` exactamente.

## Acceso y trazabilidad

Perfiles menú: **ninguno** (no hay entrada).  
Rol funcional PL/SQL: **ninguno**.  
Prueba negativa: POST sin JWT → 401. Flag off → 0 filas tocadas.  
Auditoría: DELETE `turno` sin `TBL_AUD_*` → **`diferido(auditoria)`**. Riesgo: archivo sin valor viejo/nuevo campo a campo. `hist_turno` no lo escribe este SP.

## Presupuesto no funcional (paso 3)

| Eje | Presupuesto | Cómo se mide |
|-----|-------------|--------------|
| Tiempo | p95 de una corrida ≤ 5 s en el dataset de prueba | percentil, no promedio |
| Volumen | `COUNT(*)` `turno` con `fecha_hora_tur_ini < trunc(hoy)` del orden del dump; si no hay masa → «medido en vacío» + `diferido(perf-volumen)` | nunca PASS con 1 fila si el dump tiene más |
| Concurrencia | dos actores (job + otorgar/liberar) sobre el mismo `id_turno` | **replicar `FOR UPDATE`**; CU multi-tabla → evidencia de **rollback** si falla a mitad |

## Decisiones

| ID | Decisión |
|----|----------|
| D-TUR-75 | Camino 1 **FIRME**: portar archivo + cola 6 h + disparador Api; no SCHEDULER; no T7 plantillas; no disconnect; expire 1 h se reusa. |

## Fuera de alcance (no silencio)

| Capacidad | Destino |
|-----------|---------|
| Alta + despacho REPROGRAMACION / 7 d | T7 avisos |
| `CheckHabTurnosJob` | T2 / D-TUR-11 |
| ABM frecuencia / `ACTIVA` | `diferido(disparador)` (satélite sin owner) |
| Expire TV 1 h | ya en `LlamadoPacienteExpireScheduler` |
| T6.1–T6.5 | gate-done |
| Kern / disconnect | N/A |
