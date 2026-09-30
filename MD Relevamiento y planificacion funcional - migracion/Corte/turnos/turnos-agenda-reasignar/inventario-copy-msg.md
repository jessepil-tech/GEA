---
title: Inventario copy — T6.1 reasignar
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-reasignar.copy
---

# Inventario copy — Reasignar agenda (G0)

Fuente: `HOSPITAL_2/.../Resources.properties` + `MessageBundle.TURNO_PENDIENTE_LIBERAR`.  
Web (G4): `turnos-agenda-labels.ts`.

| msg.key / constante | Valor | En UI este slice |
|---------------------|-------|------------------|
| `REASIGNAR` | REASIGNAR | menú fila (si no overlay) |
| `CANCELAR_REASIGNACION` | CANCELAR REASIGNACIÓN | menú fila (si overlay origen) |
| `TURNO_PENDIENTE_LIBERAR` | El Turno quedará pendiente de ser liberado hasta que se asigne un nuevo turno. | toast INFO al click Reasignar |
| `observaciones` | Observaciones | header popup |
| `observaciones_reasignar_turno` | Observaciones Reasignar Turno | label textarea |
| `aceptar` | Aceptar | popup |
| `cancelar` | Cancelar | popup |
| `TURNO_ASIGNADO_EXITO` | (T5) | toast al cierre otorga — reusar T5 |

Fuera de copy v1: `REASIGNAR_TURNO` (cola `turnosAReasignar.xhtml`).
