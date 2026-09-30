---
title: Inventario validaciones — T5 agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-otorgar.validaciones
---

# Inventario validaciones — T5 agenda (G0)

Plantilla gate: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.10+.

Fuente:

- BB: `BBAgenda` · `BBAsignacionTurnos` (solo flujo agenda / infoTurno / sobreturno)
- Mensajes: `HOSPITAL-BUSINESS/.../MessageBundle.java` (textos inline ES)
- SP: `TS.TURNOS.f_get_grilla_dia` · `f_reserva_turno_pac` · `f_tomar_turno_pac` · `f_otorga_turno_pac` · `f_libera_turno_pac` · `f_reserva_sobreturno_pac` · `f_liberar_turno_reservado`

Web destino: `turnos-agenda-validation.ts` + toast · API: `TurnosAgendaValidation` (G2–G4).

**Feedback UI:** WARN/ERROR → toast; INFO éxito → toast. Modal libera (no `window.confirm`). Error API en pantalla sin `/500`.

## Consultar grilla

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin convenio | Debe seleccionar un Convenio (`REQUIRED_CONVENIO_ERROR`) | G5 | G2 |
| Sin prestación | Se debe seleccionar una prestación. (`REQUIRED_PRESTACION`) | G5 | G2 |
| Sin servicio/personal/equipo según filtros | Debe seleccionar un servicio, un profesional o un equipo. (`REQUIRED_SERVICIO_PERSONAL_EQUIPO`) | G5 | G2 |
| Sin centro (otros centros) | Debe elegir el Centro de Atención. (`CENTRO_ATENCION_REQUIRED_ERROR`) | G5 | G2 |
| Rango hora inválido | El horario hasta debe ser posterior al horario desde (`WRONG_INTERVAL_HOUR`) | G5 | G2 |
| Convenio no vigente | Convenio no vigente (`CONVENIO_NO_VIGENTE_ERROR`) | G5 | G2 |
| Convenio suspendido | Convenio con atención suspendida (`CONVENIO_ATENCION_SUSPENDIDA_ERROR`) | G5 | G2 |
| Plan suspendido | Plan convenio suspendido (`PLAN_CONVENIO_ATENCION_SUSPENDIDA_ERROR`) | G5 | G2 |
| Prestación no pactada | Prestación no pactada + leyenda plan (`PRESTACION_NO_PACTADA`) | G5 INFO/WARN | G2 |

## Reservar (ASIGNAR)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin paciente | Debe seleccionar un paciente. (`PACIENTE_REQUIRED` / `DEBE_SELECCIONAR_UN_PACIENTE`) | G5 | G3 |
| Slot no libre / ocupado | Turno ocupado (`TURNO_OCUPADO`) | G5 | G3 |
| Tope personal/equipo/servicio día | Límite turno personal/equipo/servicio (`LIMITE_TURNO_*`) | G5 | G3 |
| Tope convenio/plan | Límite convenio/plan (`LIMITE_TURNO_CONVENIO_*` / `PLAN_*`) | G5 | G3 |
| Turno no otorgable | No se puede otorgar/modificar (`TURNO_OTORGADO_ERROR`) | G5 | G3 |
| Hab personal atención | El personal no está habilitado… (`EL_PERSONAL_NO_ESTA_HABILITADO_TURNOS_ATENCION`) | G5 | G3 |
| **Lock otro operador < 3 min** | El turno esta siendo tomado por otro usuario (SP -20000) | G5 | G3 |
| Paciente turnos hoy / edad | Popups edad/turnos hoy → continuar (`el_paciente_tiene_turnos…`) | G5 modal | N/A confirm |
| Éxito reserva → abre info turno | El Turno fue asignado con éxito. (`TURNO_ASIGNADO_EXITO`) | G5 INFO toast | N/A (abre dialog) |

## Tomar / lock (opción A · D-TUR-28)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Poll 59 s popup info turno | Renueva `session_id`/`fecha_session` | G5 timer → `POST /tomar` | G3 |
| Otro operador lock activo | El turno esta siendo tomado por otro usuario | G5 toast | G3 |
| Turno no encontrado / no RESERVADO | No se pudo recuperar el turno nro. … (SP -20100) | G5 | G3 |

## Otorgar

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Fecha prescripción requerida | Debe ingresar fecha prescripción (`FECHA_PRESCRIPCION_REQUIRED`) | **diferido** hijo cobros | **diferido** |
| Fecha prescripción vencida | Prescripción vencida (`FECHA_PRESCRIPCION_*`) | **diferido** hijo | **diferido** |
| Teléfono obligatorio paciente | Debe ingresar teléfono (`DEBE_INGRESAR_TELEFONO`) | **diferido** ficha mínima v1 stub | **diferido** |
| Lock otro operador | El turno esta siendo tomado por otro usuario | G5 | G4 |
| Éxito otorgar | El Turno fue asignado con éxito. (`TURNO_ASIGNADO_EXITO`) | G5 INFO toast | G4 |

## Liberar

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin motivo | Debe seleccionar motivo liberación (`REQUIRED_MOTIVO_LIBERACION`) | G5 modal | G4 |
| Éxito | Liberado con éxito (`TURNO_LIBERADO_EXITO`) | G5 INFO toast | G4 |
| Reasignación pendiente | Turno pendiente liberar (`TURNO_PENDIENTE_LIBERAR`) | **diferido T6** | **diferido** |

## Sobreturno

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Prestación requerida | Prestación requerida (`PRESTACION_REQUIRED_INFO`) | G5 | G4 |
| Servicio requerido | Debe elegir el Servicio. (`SERVICIO_REQUIRED_ERROR`) | G5 | G4 |
| Centro/servicio/prestación/fecha/hora | `REQUIRED_CENTRO_ATENCION` / `REQUIRED_SERVICIO` / `FECHA_MENOR_ACTUAL` / `HORA_INVALIDA_ERROR` | G5 | G4 |
| Hab pers/equipo/serv sobreturno | `EL_*_NO_ESTA_HABILITADO_TURNOS_ATENCION` | G5 | G4 |

## Expiración reservas (G3)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| RESERVADO > 3 min sin renovar | `f_liberar_turno_reservado` → libera | silent | G2 GET grilla + G3 POST expirar |
| Sobreturno RESERVADO > 30 min | DELETE turno | silent | idem |

## Elegibilidad WS

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Nro afiliado/doc elegibilidad | `NRO_AFILIADO_ELEGIBILIDAD_REQUIRED` / `NRO_DOCUMENTO_ELEGIBILIDAD_REQUIRED` | G5 (control icon) | **diferido** `turnos-agenda-elegibilidad-cobros` · P-ORA-010 |

## Módulo / gates

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Call center | Debe seleccionar call center (`CALL_CENTER_REQUIRED_ERROR`) | T1 `/turnos/inicio` | T1 JWT |

## Checklist anti-omisión

- [ ] Lock A: golden choque 3 min + IT dos operadores
- [ ] Mensaje SP lock = texto exacto legacy en UI+API
- [ ] Reservar **y** otorgar comparten reglas de tope/hab
- [ ] Popups cobros/prescripción/teléfono: filas **diferido** explícitas (no silencio)
- [ ] Reasignar/repetidos/imprimir: no validar en T5 v1
