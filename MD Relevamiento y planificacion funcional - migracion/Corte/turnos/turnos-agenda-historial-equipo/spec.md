---
title: Spec — Equipo en Historial de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-historial-equipo.spec
---

# Spec — Equipo en Historial de Agenda

## Semilla

`asignacionTurnos/historialTurno.xhtml`. Índice 2026-09-28: 2/8 xhtml, 5/15 beans, 0 firmas, 3/5 rptdesign.

Entra solo el combo Equipo del north. El chrome de Agenda, los buscadores y los tres reportes ya están cerrados. `--jobs historial`: el corte padre no encontró jobs de esta hoja. **N/A**.

## Clarify

| # | Pregunta | Respuesta | Estado |
|---|----------|-----------|--------|
| 1 | ¿Pipeline? | Historial gate-done con el combo disabled. Agenda ya lista equipos con hab vigente. | en curso |
| 2 | ¿Happy path? | Historial → centro 1001 → Equipo `EQUIPO DEMO` → Consultar muestra el hist `9676753`. | en curso |
| 3 | ¿Ciclo de vida? | La hoja no cambia estado. Lee `ts.hist_turno`. | en curso |
| 4 | ¿Errores? | Horas invertidas siguen el contrato de Historial. Elegir equipo limpia el profesional, y al revés. | en curso |
| 5 | ¿Side-effects? | Ninguno. El Excel de la lista usa el mismo filtro. | en curso |
| 6 | ¿Fuera? | Menú `consultaHistorialTurno`. Suspender, cola, consulta, sobreturno y múltiples. | hermanas |
| 7 | ¿Paridad UI? | El combo ya está en la fila del profesional, `InputWid100`. Se habilita. No se redibuja Historial. | inventarios |
| 8 | ¿Viaje Playwright? | **e2e-migrado**: abrir Historial y ver el combo habilitado. La fila del equipo, al evidenciar. | verify |

## Presupuesto no funcional

| Eje | Presupuesto |
|-----|-------------|
| Tiempo | p95 de Consultar con equipo, a medir al evidenciar. Default de grilla ≤ 2 s |
| Volumen | **medido en vacío** + `diferido(perf-volumen)` |
| Concurrencia | **N/A**. SELECT de historial, sin `FOR UPDATE` |

Instalación: Call Center Demo. Sin ramas `esClienteX()`.
