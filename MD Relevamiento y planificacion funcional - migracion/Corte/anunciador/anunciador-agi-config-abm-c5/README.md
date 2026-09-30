---
title: Pagaré — P3 C5 smoke ABM anunciador
status: deferred
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.anunciador-agi-config-abm-c5
---

# P3 C5 smoke — `anunciador-agi-config-abm-c5`

Padre: [`anunciador-agi-config-abm/`](../anunciador-agi-config-abm/)  
Estado: **diferido** (pagaré) · 2026-09-16

C5 del padre (smoke ops G6 + click-through residual + Playwright) **no se cobra** en esta oleada. Motivo: el picker de esta semana pasa a Maestros M1; C5 no tenía pedido de operador.

**Gatillo de cobro:** pedido explícito de smoke ops, o al retocar el ABM anunciador. Hasta entonces el satélite **no** se declara gate-done.

Spec/plan/tasks: no abiertos (sin código nuevo). Escritura ya cobrada en el padre: `ts.anunciador` id=3.
