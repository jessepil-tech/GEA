---
title: Diferido — filtros Equipo fuera de la Agenda
description: Hojas que dejaron el filtro Equipo apagado y no entran en turnos-agenda-equipo.
version: 0.1.0
status: deferred
owner: grupogea
last_updated: 2026-09-24
phase_id: sdd.hospital.turnos-agenda-equipo-hermanas
---

# turnos-agenda-equipo-hermanas (diferido)

No arranca junto con el combo de Agenda: cada una es otra semilla y la unión pasa el techo de 8 xhtml.

| Hoja | Por qué queda afuera |
|------|----------------------|
| Historial | **gate-done** 2026-09-28: [`turnos-agenda-historial-equipo`](../turnos-agenda-historial-equipo/) |
| Suspender y quitar suspensión | **gate-done** 2026-09-28: [`turnos-grilla-suspender-equipo`](../turnos-grilla-suspender-equipo/) |
| Cola de reasignar | **gate-done** 2026-09-28: [`turnos-agenda-cola-equipo`](../turnos-agenda-cola-equipo/) |
| Consulta de agenda | **gate-done** 2026-09-28: [`turnos-agenda-consulta-equipo`](../turnos-agenda-consulta-equipo/) |
| Sobreturno | **gate-done** 2026-09-28: [`turnos-agenda-sobreturno-equipo`](../turnos-agenda-sobreturno-equipo/) |
| Turnos múltiples | **gate-done** 2026-09-28: [`turnos-agenda-multiples-equipo`](../turnos-agenda-multiples-equipo/) |

Se abre un slug por hoja cuando se cobre, no un módulo entero.
