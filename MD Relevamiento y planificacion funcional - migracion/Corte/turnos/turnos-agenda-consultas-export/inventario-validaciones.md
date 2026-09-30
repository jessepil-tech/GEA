---
title: Inventario validaciones — T5.5 hijo Excel
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export.validaciones
---

# Inventario validaciones — Excel Consulta Agenda

HIS **no** revalida fechas en `generarReporteExcel` (usa `listTurnos`). Camino 1: Web reusa `validarFechas` del Consultar sobre `lastFiltro`; Api reusa `validateConsultaRango`.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Lista vacía | noop | no request, no toast | 204 |
| fechaHasta ≥ fechaDesde | implícito (ya consultó) | toast `_3` si north inválido antes de exportar | 400 `_3` |
| fechaDesde ≥ hoy | implícito | toast `_9` | 400 `_9` |
| Call center | sesión T5 | gate Agenda | T5 |
