---
title: Spec — Combo Equipo en Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-equipo.spec
---

# Spec — Combo Equipo en Agenda

## Semilla

`asignacionTurnos/agenda.xhtml`. Índice 2026-09-24:

| Techo | Medido |
|-------|--------|
| xhtml | 3 / 8 |
| beans | 4 / 15 |
| firmas | 0 / 20 |
| rptdesign | 3 / 5 |

Los tres reportes (`PreparacionPrevia`, `Turno`, `TurnoTicket`) ya están en hijos cerrados. Este corte no los reabre.

`--jobs agenda`: sin coincidencias. **N/A**.

## Clarify — **FIRME** (ealbo, 2026-09-28)

| # | Pregunta | Respuesta | Estado |
|---|----------|-----------|--------|
| 1 | ¿Pipeline? | La grilla EQUIPO ya existe (`id_turno=17255316`). Falta el combo del north para elegirla. | FIRME |
| 2 | ¿Happy path? | Agenda → combo Equipo `EQUIPO DEMO` → el día 29/11 muestra el turno LIBRE → otorgar sobre ese hueco. | FIRME |
| 3 | ¿Ciclo de vida? | El hueco nace LIBRE en el tramo 1. Otorgar lo pasa a OTORGADO con el paciente, igual que personal o servicio. | FIRME |
| 4 | ¿Errores? | Sigue el contrato de Agenda: hace falta servicio, profesional **o** equipo. | FIRME |
| 5 | ¿Side-effects? | Escribe `ts.turno` del hueco. Sin mail nuevo. | FIRME |
| 6 | ¿Fuera? | Historial, suspender, cola, consulta de agenda, sobreturno y turnos múltiples. Reportes del ticket. Jobs. | hermanas |
| 7 | ¿Paridad UI? | El combo ya está en la fila Profesional \| Equipo, `InputWid100`, hoy `disabled`. Se enciende. No se redibuja la Agenda. | inventarios |
| 8 | ¿Viaje Playwright? | **e2e-migrado**: elegir equipo y ver el hueco del 29/11. Aún no corrido. | verify |

### Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-17 tramo 2 | Este slug es solo el combo de `agenda.xhtml` y el otorgar sobre un hueco EQUIPO. |
| Hermanas | [`turnos-agenda-equipo-hermanas`](../turnos-agenda-equipo-hermanas/) |
| Reportes | N/A — hijos de impresión ya cerrados |
| Instalación | Call Center Demo. Sin ramas `esClienteX()` en esta hoja |

## Presupuesto no funcional

| Eje | Presupuesto |
|-----|-------------|
| Tiempo | p95 de listar el día con equipo elegido, a medir al evidenciar |
| Volumen | **medido en vacío** (un equipo, un hueco) + `diferido(perf-volumen)` |
| Concurrencia | Dos otorgar sobre el mismo `id_turno`. Estrategia = la de T5 (sin `FOR UPDATE` nuevo) |

## Acceso

El mismo menú TURNOS de Agenda. Rol funcional: el que ya exige otorgar en T5. La prueba negativa es un actor sin esa entrada.
