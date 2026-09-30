---
title: Inventario validaciones — T6.1 reasignar
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-reasignar.validaciones
---

# Inventario validaciones — Reasignar (G0)

Fuente: `BBAgenda.actBtnReemplazarTurno` / `actBtnCancelarReasignacionTurno` · `BBAsignacionTurnos` otorga con sesión · menú xhtml.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Reasignar solo si `modificable` y hay paciente | menú `disabled` | igual | no comando sesión |
| Toast al entrar en reasignar | `TURNO_PENDIENTE_LIBERAR` INFO | toast | no write |
| Overlay no persiste | DTO `PENDIENTE_LIBERAR` | pintura | origen sigue `OTORGADO` hasta cierre |
| Cancelar reasignación | `removeSessionValue` | limpia; no libera BD | no write |
| Cierre: mismo paciente en north | HIS carga paciente del origen | north = origen | otorga T5 + `idTurnoOrigen` |
| Popup obs si hay sesión y flag ausente | `$popupObservacionesReasignarTurno` | modal; Aceptar sigue; Cancelar no borra sesión origen | texto obs en otorga |
| Liberar origen | `otorgarTurnoPac(turno, turnoAReasignar)` | toast T5 asignado | libera origen (reusa T5) |
| Tope cantidad mismo día | HIS **omite** chequeo tope si reasignar **mismo día** | paridad T5 + esta excepción | G2 documentar |

Sin CRUD de cola `turno_a_reasignar` (eso es T4/T6). Error API en toast, **sin** `/500`.
