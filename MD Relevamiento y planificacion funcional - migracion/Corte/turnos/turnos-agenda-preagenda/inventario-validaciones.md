---
title: Inventario validaciones — T5.6 pre-agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-preagenda.validaciones
---

# Inventario validaciones — Pre-agenda (G0)

Fuente: `BBPreAgendaTurnos.actBtnConsultar`.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| fechaHasta ≥ fechaDesde | `WRONG_INTERVAL_DATE_3` | toast | 400 mismo texto |
| fecha ≥ hoy | **No** (HIS no valida `_9`) | sin min date | no 400 `_9` |
| Default fechas | hasta = sysdate; desde = −3 meses | ctor | — |
| Call center | sesión T5 | gate Agenda | T5 |
| Filtros opcionales | combos Todos; paciente vacío | vacío = no filtra | null = no filtra |
| Estado lista | hardcode `PENDIENTE` | no combo estado | siempre PENDIENTE |
| Convenio / plan | Example + EXISTS prescrip amb/int | combos | mismo; plan solo si convenio |
| Equipo | no está en esta hoja | — | — |

Error API en toast, **sin** `/500`.
