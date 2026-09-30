---
title: Inventario validaciones — equipo en suspender
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-grilla-suspender-equipo.validaciones
---

# Validaciones

| Regla | Mensaje | Dónde |
|-------|---------|--------|
| Radio Equipo sin equipo elegido | Debe seleccionar un equipo. | toast, antes de Consultar |

El padre ya cubre motivo obligatorio, turno ocupado y el resto del SP. Este corte no los reabre.
