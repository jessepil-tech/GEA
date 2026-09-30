---
title: Spec — Equipo en la cola de reasignar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-cola-equipo.spec
---

# Spec — Equipo en la cola de reasignar

## Semilla

turnosAReasignar.xhtml. El padre ya cerró la hoja. Entra solo el filtro Equipo.

Jobs de esta hoja: **N/A**, el padre no los encontró en este circuito.

## Clarify

| # | Pregunta | Respuesta | Estado |
|---|----------|-----------|--------|
| 1 | ¿Pipeline? | La hoja gate-done dejó el filtro Equipo apagado. | cerrado |
| 2 | ¿Happy path? | Cola de reasignar con `EQUIPO DEMO`. | cerrado |
| 3 | ¿Fuera? | Las otras hermanas, cada una en su slug. | cerrado |
| 4 | ¿Paridad UI? | El control ya está. Se habilita. No se redibuja la hoja. | inventarios |
| 5 | ¿Viaje Playwright? | **e2e-migrado**. | verify |

## Presupuesto no funcional

| Eje | Presupuesto |
|-----|-------------|
| Tiempo | p95 ≤ 2 s |
| Volumen | **medido en vacío** + `diferido(perf-volumen)` |
| Concurrencia | **N/A**. Este filtro no agrega `FOR UPDATE` |

Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()`.
