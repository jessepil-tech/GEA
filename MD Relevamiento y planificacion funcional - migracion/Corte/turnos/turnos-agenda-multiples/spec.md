---
title: Spec — turnos múltiples agenda
version: 1.0.0
status: proposed
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-multiples
---

# Spec — Turnos múltiples

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/).  
Ruta: **`/turnos/agenda`**.  
Clarify **FIRME** Camino 1 — 2026-09-11 (Francisco «ok firme»). D-TUR-55 · D-TUR-56.

Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

## Problema

HIS, desde Turnero **Turnos Múltiples**, abre `turnosMultiples.xhtml`: carrito de N prestaciones (pueden ser otro centro/servicio/profesional), Consultar pinta días donde el combo entra, la tabla es el juego de huecos LIBRE del día, y **Asignar Turno** reserva el lote y abre infoTurno. Web T5 deja el ítem disabled.

## Resultado (objetivo Camino 1)

Mismo `/turnos/agenda`. Turnero habilita la vista (north T5.1 + Agregar + tablas; west calendario 260px). Consultar no escribe. Asignar Turno reserva el lote (un TX) e infoTurno tabla T5.3 otorga N o Cancelar libera N.

## Clarify — **FIRME** Camino 1 (2026-09-11)

| # | Pregunta | Respuesta | Evidencia |
|---|---------|-----------|-----------|
| 1 | ¿Pipeline? | T5 reserva/otorga/libera **gate-done**. North paciente/convenio T5.1. Oferta T4. Sin `tmp_turno*` en PG (filtros JSON). Ports Java: días, grilla del día, reserva lote. | `f_get_dias_disp_multiple` · `f_get_grilla_multiple_dia` · `f_reserva_turno_multiple_pac` |
| 2 | ¿Misma ruta? | HIS navega a `turnosMultiples.faces`. **Camino 1:** vista en **`/turnos/agenda`** (reemplaza la grilla, no tile). Accordion Turnero 170px ya está. | `asignacionTurnos.xhtml` L69–71 |
| 3 | ¿Happy path? | Turnero **Turnos Múltiples** → north prestación/centro/servicio/profesional + **Agregar** → **Consultar** (pide convenio) → calendario días + tabla slots → **Asignar Turno** (si >1 centro, popup 450px) → reserva lote → infoTurno **tabla** T5.3 → otorga N **o** Cancelar libera N. | xhtml L218–316 · BB `actBtnAceptarTurnosMultiples` · `actionBtnReservarTurnosMultiples` |
| 4 | ¿Ciclo de vida? | Hasta Asignar: solo LIBRE en sesión. Reserva = un TX. Cancelar infoTurno = libera el lote (T5). Volver west Acciones / Limpiar: sin escritura de reserva. | SP reserva · infoTurno L287–295 |
| 5 | ¿Errores? | Prestación vacía al Agregar; convenio vacío al Consultar; sin slots; mismo horario; turno ocupado. Toast Api sin `/500`. | MessageBundle · SP `-20200` |
| 6 | ¿Fuera? | `$popupCambiarTurno` (swap in-memory) → [`turnos-agenda-multiples-cambiar/`](../turnos-agenda-multiples-cambiar/). Equipo usable **D-TUR-17** (visible disabled). Pre-agenda. Consultas. T7 mail. Print prep (solo recepción). HOS-APP. | xhtml L302–303 · L540 |
| 7 | ¿Gate UI? | Inventarios **antes** de template. North form + tabla filtros `scrollHeight=151` + Consultar/Limpiar + tabla turnos 151 + south Asignar. West 260: calendario + desde/hasta + ficha. Popup centro `width=450` `closable=false`. Look Origin / geometría HIS. | `turnosMultiples.xhtml` · regla v1.14 |
| 8 | ¿Playwright? | **e2e-migrado:** Turnero → 2 filtros fixture → Consultar → día → Asignar → infoTurno. Convenio faltante → toast. Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API? | POST días + POST grilla (filtros en body). POST `…/reservar-multiples` (lote, un TX). Otorgar/liberar reusa T5. Resource delgado. | scaffold T5.3 `reservar-repetidos` |

**Camino 1** = menú + carrito + consultar/calendario + reserva lote + infoTurno.  
No hay Camino “solo habilitar el ítem”: HIS reserva en el mismo acto.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Ítem Turnero Múltiples | `asignacionTurnos.xhtml` L69–71 | **In scope** | No |
| North + Agregar al carrito | xhtml L24–270 | **In scope** | No |
| Consultar días | `f_get_dias_disp_multiple` | **In scope** | No |
| Tabla slots del día | `f_get_grilla_multiple_dia` | **In scope** | No |
| Popup centro si >1 | xhtml L506–529 | **In scope** | No |
| Asignar → reserva lote | `f_reserva_turno_multiple_pac` | **In scope** | UPDATE N `RESERVADO` |
| infoTurno tabla + otorga N | `otorgaTurnoMultiple` · infoTurno L167 | **In scope** | otorga T5 |
| Cancelar infoTurno libera N | infoTurno L293 | **In scope** | T5 |
| Icono cambiar horario | xhtml L302 · `$popupCambiarTurno` | **done** [`turnos-agenda-multiples-cambiar/`](../turnos-agenda-multiples-cambiar/) | No (hasta Asignar) |
| Equipo usable | combo | **diferido** D-TUR-17 | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Turnero Múltiples enabled; vista en `/turnos/agenda`. |
| RF-2 | Agregar prestación al carrito (north no se limpia); trash quita fila y reconsulta o limpia; Limpiar vacía carrito+slots+calendario. |
| RF-3 | Consultar pinta calendario; click día llena tabla slots. |
| RF-4 | Asignar Turno reserva lote (un TX) e abre infoTurno tabla; >1 centro → popup 450px. |
| RF-5 | Cancelar infoTurno libera el lote. |
| RF-6 | Gate UI xhtml **antes** de template. |
| RF-7 | e2e 2 filtros + convenio faltante. |
| NFR-1 | Resource delgado; schema `ts`; sin `tmp_*`; no clonar PL/pgSQL. |

## Decisiones **FIRME**

| Id | Decisión |
|----|----------|
| D-TUR-55 | Vista en `/turnos/agenda` (no tile). Lectura = POST días/grilla con filtros JSON. Escritura = POST `reservar-multiples` (port Java, un TX). |
| D-TUR-56 | Asignar = reserva lote + infoTurno tabla T5.3. Cambiar horario fuera (hijo). Equipo visible disabled. |
