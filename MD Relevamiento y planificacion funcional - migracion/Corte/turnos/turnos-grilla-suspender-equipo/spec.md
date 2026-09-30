---
title: Spec — Equipo en Suspender y Quitar suspensión
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-grilla-suspender-equipo.spec
---

# Spec — Equipo en Suspender y Quitar suspensión

## Semilla

`suspenderGrillaTurnos.xhtml` y `quitarCancelacionGrillaTurnos.xhtml`. El padre ya cerró las dos hojas. Entra solo el radio Equipo.

`--jobs` de estas hojas: el padre las marcó **N/A** (no arman la suspensión). Sigue **N/A**.

## Clarify

| # | Pregunta | Respuesta | Estado |
|---|----------|-----------|--------|
| 1 | ¿Pipeline? | T6.4 gate-done con el radio Equipo disabled. La lista de equipos es la de Agenda. | cerrado |
| 2 | ¿Happy path? | Radio Equipo → `EQUIPO DEMO` → Consultar → suspender `17255287` → quitar y vuelve `LIBRE`. | cerrado |
| 3 | ¿Ciclo de vida? | `LIBRE` o `OTORGADO` → `SUSPENDIDO` → `LIBRE`. El padre ya lo portó. | cerrado |
| 4 | ¿Errores? | Sin equipo: «Debe seleccionar un equipo.» El resto es el contrato del padre. | cerrado |
| 5 | ¿Side-effects? | Lote `suspension_agenda_turnos` e `hist_turno`, como el padre. | cerrado |
| 6 | ¿Fuera? | Cola, consulta, sobreturno, múltiples. Mail/SMS. Reemplazo. | hermanas |
| 7 | ¿Paridad UI? | El radio ya está en la fila de opciones. Se habilita. No se redibuja la hoja. | inventarios |
| 8 | ¿Viaje Playwright? | **e2e-migrado**: las dos hojas muestran el radio habilitado y el combo. La escritura, al evidenciar. | verify |

## Presupuesto no funcional

| Eje | Presupuesto |
|-----|-------------|
| Tiempo | p95 de Consultar con equipo, a medir al evidenciar. Default de grilla ≤ 2 s |
| Volumen | **medido en vacío** + `diferido(perf-volumen)` |
| Concurrencia | **N/A**. El padre ya firmó que este SP no usa `FOR UPDATE` |

Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()`.

`f_suspender_turnos` y `f_quitar_suspension`: **rediseñar** ya firmado en el padre (JDBC). Este corte no abre otra firma.
