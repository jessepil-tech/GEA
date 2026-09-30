---
title: Plan — Combo Equipo en Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-equipo.plan
---

# Plan

1. Firma de ealbo sobre el Clarify. Hecha 2026-09-28.
2. Checkout de `dev/tur-agenda-equipo` en Api, Web y Migration. Hecho. Sin push.
3. Gate UI: encender el `select` que ya está en el north. No rehacer la Agenda. Hecho en la rama.
4. Api: listar equipos con hab del centro/servicio y filtrar la grilla del día por `cod_item_equipo`. Otorgar el hueco EQUIPO con el mismo camino de T5. Hecho en la rama.
5. Web: el combo deja de estar `disabled` y carga `EQUIPO DEMO`. Hecho en la rama.
6. Evidencia: viaje, fila `17255287`, acceso, p95 588 ms y par sobre `17255288` (A 200, B 404). Hecho 2026-09-28. Volumen diferido.
