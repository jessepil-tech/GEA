---
title: Inventario validaciones — T5.2 sobreturno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno.validaciones
---

# Inventario validaciones — Sobreturno (G0)

Fuente: `BBAgenda.actBtnSobreturno` / `actBtnOtorgarSobreturno` / `otorgarSobreTurno` · `TurnosAgendaValidation.validateReservarSobreturno`.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Paciente requerido para abrir | toast `DEBE_SELECCIONAR_UN_PACIENTE` | igual; no popup | no |
| Prestación | `REQUIRED_PRESTACION` | toast | `REQUIRED_PRESTACION` (T5) |
| Centro | `REQUIRED_CENTRO_ATENCION` | toast | `CENTRO_ATENCION_REQUIRED_ERROR` (T5) |
| Servicio | `REQUIRED_SERVICIO` | toast | `Debe elegir el Servicio.` (T5) |
| Convenio/plan | north previo (otorga) | north T5.1 | API sobreturno exige ids (T5) |
| Fecha ≥ hoy | `FECHA_MENOR_ACTUAL` | calendar mindate + toast | G2 si falta |
| Hoy: hora desde ≥ ahora | `HORA_INVALIDA_ERROR` | toast + inline; **no** aplica a fecha futura | G2 si falta |
| Hasta > desde | `onChangeHoraDesde` +30 | UI | `WRONG_INTERVAL_HOUR` (T5) |
| Sin grilla del día | `NO_SE_PUEDE_ASIGNAR_SOBRETURNO` (SP Oracle; Java llama al SP) | **no** bloquear: sobreturno es INSERT extra, no hueco LIBRE. GET grilla solo para turnos-de-hoy | Adapter T5 **no** porta el check SP |
| Call center | T5 | sesión inicio | T5 `idCallCenter` |
| Motivo | opcional si combo vacío | combo si seed | `idMotivoSobreturno` nullable T5 |
| Lista espera hab | `EL_*_NO_ESTA_HABILITADO_TURNOS_ATENCION` | **fuera** | — |
| Tope SOBRETURNOS | `pp_ctrl_turnos_pac` | **diferido** | — |
| Lock / otorga | infoTurno T5 | reusa | reusa |

Error API en toast, **sin** `/500`.
