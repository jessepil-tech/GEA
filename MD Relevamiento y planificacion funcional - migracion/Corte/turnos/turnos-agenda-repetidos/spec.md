---
title: Spec — turnos repetidos agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos
---

# Spec — Turnos repetidos

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/).  
Ruta: **`/turnos/agenda`**.  
Clarify **FIRME** Camino 1 — 2026-09-10 (Francisco «ok firme»). D-TUR-51 · D-TUR-52.

Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

## Problema

HIS, desde el menú gear **TURNOS REPETIDOS**, abre `turnosRepetidos.xhtml`: popup de días + cantidad, reserva N slots (`f_reservar_turnos_repetidos`), lista reservados / observaciones, y **Asignar Turnos** entra a infoTurno con la tabla de repetidos. Web T5 no tiene el ítem ni el acto.

## Resultado (objetivo Camino 1)

Mismo módulo TURNOS. Menú fila → popup días/ctd → reservar → tablas → Volver (libera) o Asignar Turnos (infoTurno tabla + otorga todos, reusa T5). Concat prep/req de las filas al abrir infoTurno.

## Clarify — **FIRME** Camino 1 (2026-09-10)

| # | Pregunta | Respuesta | Evidencia |
|---|---------|-----------|-----------|
| 1 | ¿Pipeline? | T5 reserva/otorga/libera **gate-done**. Prestación + paciente + convenio de north (T5.1). Oferta T4. SP `f_reservar_turnos_repetidos` no está en Api → **G2 port CQRS** (no clonar PL/pgSQL). `ts.tmp_observ_tur_rep` no está en Flyway → **G1** o devolver observaciones en el result set. | T5 · package TURNOS L5335 |
| 2 | ¿Misma ruta? | HIS navega a `turnosRepetidos.faces` (template `asignacionTurnos`). **Camino 1:** vista en **`/turnos/agenda`** (reemplaza grilla, no tile nuevo). | `BBAgenda.actBtnTurnosRepetidos` L2446–2460 |
| 3 | ¿Happy path? | Gear TURNOS REPETIDOS (enable HIS) → popup `$popupTurnosRepetidos` (días + Ctd. Turnos) → Asignar Turno **reserva** → tablas Turnos Reservados + Observaciones → Asignar Turnos → infoTurno con tabla xhtml L164 → otorga N (T5) **o** Volver libera N y vuelve a la grilla. | `turnosRepetidos.xhtml` · `BBTurnosRepetidos` L120–176 |
| 4 | ¿Ciclo de vida? | Reserva: N filas `RESERVADO` (mismo paciente/prestación). Cancelar popup: no reserva. Volver agenda: `liberarTurnosPac` de los reservados. Otorgar: loop T5 (obs/fecha por fila en la tabla infoTurno). | BB L167–175 · infoTurno L164–212 |
| 5 | ¿Errores? | 0 encontrados → popup `No se encontraron turnos en los días seleccionados`. Parcial → `Se generaron {%1} turnos, {%2} no se encontraron.` Toast Api sin `/500`. | MessageBundle · xhtml L298 |
| 6 | ¿Fuera? | `$popupCambiarTurno` → [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/). Múltiples / pre-agenda. Equipo D-TUR-17 (input disabled). clienteUP north. T7 mail. | xhtml L311 |
| 7 | ¿Gate UI? | Inventarios **antes** de template. Popup `closable=false` header `Turnos Repetidos`; botones 105px Asignar Turno / Volver (no `authPrimary`). Página: north ficha disabled + center dos tablas scroll 50% + south Asignar Turnos / Volver 110px. InfoTurno tabla repetidos: cols fecha/hora/centro/servicio/profesional/coseguros/fecha prescripción/obs. | regla v1.13 |
| 8 | ¿Playwright? | **e2e-migrado:** menú → popup → reservar fixture 2 slots → tabla → otorgar. 0 encontrados → popup mensaje. Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API? | POST reserva repetidos (días + ctd + idTurno semilla) en `TurnosAgendaResource`. Liberar lote reusa T5. Otorgar N reusa T5. Resource delgado. | scaffold |

**Camino 1** = menú + popup reserva + tablas + Volver/Asignar Turnos (infoTurno tabla + otorga).  
No hay Camino “solo menú”: HIS reserva en el mismo acto.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Menú TURNOS REPETIDOS | agenda.xhtml L285 | **In scope** | No |
| Popup días + ctd | `$popupTurnosRepetidos` | **In scope** | No |
| Reservar N | `f_reservar_turnos_repetidos` | **In scope** | UPDATE N `RESERVADO` |
| Tabla reservados | xhtml L135 | **In scope** | No |
| Tabla observaciones faltantes | xhtml L168 | **In scope** | No (o tmp G1) |
| Popup 0 / parcial | L298 | **In scope** | No |
| Volver → libera N + agenda | BB L167 | **In scope** | UPDATE/DELETE T5 |
| Asignar Turnos → infoTurno tabla | L191 · infoTurno L164 | **In scope** | otorga T5 |
| Concat prep/req de las filas | BB L585–612 | **In scope** | GET ya T5.1e-q |
| Obs/fecha por fila al otorgar | infoTurno L206–211 | **In scope** | UPDATE T5 persist |
| Cambiar horario | `$popupCambiarTurno` | **done** [`turnos-agenda-repetidos-cambiar/`](../turnos-agenda-repetidos-cambiar/) | UPDATE T5 |
| Múltiples / pre-agenda | otros xhtml | **diferido** slugs T5 | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Menú enable/disable como HIS L285. |
| RF-2 | Popup días + ctd; Asignar Turno reserva. |
| RF-3 | Tablas reservados + observaciones; mensajes 0/parcial. |
| RF-4 | Volver libera los RESERVADO de este acto. |
| RF-5 | Asignar Turnos abre infoTurno con tabla; otorga todas las filas (T5). |
| RF-6 | Gate UI xhtml **antes** de template. |
| RF-7 | e2e reserva 2 + empty. |
| NFR-1 | Resource delgado; schema `ts`; no clonar package PL/pgSQL. |

## Decisiones **FIRME**

| Id | Decisión |
|----|----------|
| D-TUR-51 | Vista repetidos en `/turnos/agenda` (no tile nuevo). POST reserva = port del SP a CQRS/JDBC. |
| D-TUR-52 | Asignar Turnos = infoTurno tabla + loop otorga T5. Cambiar horario fuera. |
