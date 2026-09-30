---
title: Inventario interacción UI — T6.1 reasignar
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-reasignar
---

# Inventario interacción UI — Reasignar (G0)

Fuente: `agenda.xhtml` L279–284 · `asignacionTurnos.xhtml` L122–147 · `BBAgenda` / `BBAsignacionTurnos`.

## Menú fila (gear)

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| `p:menuitem` `REASIGNAR` | Clic; `rendered` si estado UI ≠ `PENDIENTE_LIBERAR`; disabled si `!modificable` o sin paciente | `actBtnReemplazarTurno`: sesión `TurnoAReasignar` (`setTurnoAReasignar(false)`); north = paciente del turno; toast `TURNO_PENDIENTE_LIBERAR`; overlay origen | Misma fila gear T5; **no** botón inline |
| `p:menuitem` `CANCELAR_REASIGNACION` | Clic; `rendered` si estado UI = `PENDIENTE_LIBERAR` | `actBtnCancelarReasignacionTurno`: `removeSessionValue("TurnoAReasignar")`; refresca grilla | Limpia sesión Web |
| Overlay origen | `initTurnos` si sesión y `!isTurnoAReasignar()` | `setEstadoTurno("PENDIENTE_LIBERAR")` **solo DTO** | Pintura fila; no PATCH estado |

## Cierre (mismo paciente → LIBRE)

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| ASIGNAR_TURNO sobre `LIBRE` | Flujo T5 reserva→infoTurno→otorgar, con sesión origen | North ya es el paciente origen | Reusar T5 |
| `$popupObservacionesReasignarTurno` | Otorgar si hay `TurnoAReasignar` y aún no `MostrarObsReasignarTurno` | Modal 650, sin X, header Observaciones | Mostrar **si HIS lo pide** |
| Aceptar popup | `actBtnAceptarObservacionesReasignarTurno` | Cierra y sigue `actionBtnOtorgarTurno` | Continúa otorga |
| Cancelar popup | `actBtnCancelarObservacionesReasignarTurno` | Cierra; quita flag obs; **sesión origen sigue** | No limpia reasignación |
| Otorga con origen | `otorgarTurnoPac(turno, turnoAReasignar)` | Nuevo `OTORGADO`; libera origen; limpia sesión | API origen opcional |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Columna / UPDATE `PENDIENTE_LIBERAR` en `ts.turno` | Prohibido |
| Fork `/turnos/reasignar` | Prohibido |
| Meter cola `turnosAReasignar.xhtml` | Prohibido (T6 padre) |
| Pedir smoke sin menú gear + popup HIS (header Observaciones, sin X, 650) | Prohibido |
| Liberar origen al Cancelar reasignación | Prohibido (solo limpia sesión) |
