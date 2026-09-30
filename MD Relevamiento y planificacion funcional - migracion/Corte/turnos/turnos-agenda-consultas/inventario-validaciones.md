---
title: Inventario validaciones — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-consultas.validaciones
---

# Inventario validaciones — Consulta Agenda (G0)

Fuente: `BBConsultaAgenda.actBtnConsultar`.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| fechaHasta ≥ fechaDesde | `WRONG_INTERVAL_DATE_3` | toast | 400 mismo texto |
| fechaDesde ≥ hoy | `WRONG_INTERVAL_DATE_9` · calendar `mindate` | min date + toast | 400 mismo texto |
| Call center | sesión T5 | gate Agenda | T5 |
| Filtros opcionales | combos Todos | vacío = no filtra | null = no filtra |
| Estado | `""` / `LIBRE` / `OTORGADO` | combo | mismo |
| Incluye sobreturnos | default true → `'S'` else `'N'` | check | boolean / char |
| Equipo | HIS filtra `codEquipo` | **no filtra** D-TUR-17 | no enviar |

Error API en toast, **sin** `/500`.
