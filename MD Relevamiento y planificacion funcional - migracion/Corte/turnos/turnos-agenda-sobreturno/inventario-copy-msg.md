---
title: Inventario copy — T5.2 sobreturno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno.copy
---

# Inventario copy — Sobreturno agenda (G0)

Fuente: `Hospital-Legacy/HOSPITAL_2/src/ar/com/thinksoft/resources/Resources.properties` + `MessageBundle`.  
Web destino: `turnos-agenda-labels.ts` (extender; no inventar copy).

| msg.key / constante | Valor | En UI este slice |
|---------------------|-------|------------------|
| `sobreturno` | Sobreturno | header popup + ítem west + leyenda grilla (leyenda **ya** T5) |
| `prestacion` | Prestación | label + `(*)` |
| `fecha` | Fecha | popup |
| `hora_desde` | Hora Desde | popup |
| `hora_hasta` | Hora Hasta | popup |
| `centro_atencion` | Centro Atención | + `(*)` |
| `servicio` | Servicio | + `(*)` |
| `profesional` | Profesional | popup |
| `equipo` | Equipo | label; combo **disabled** D-TUR-17 |
| `paciente` | Paciente | readonly |
| `motivo_sobreturno` | Motivo Sobreturno | combo si hay motivos |
| `buscar` | Buscar | title lupa profesional |
| `aceptar` | Aceptar | footer popup + turnos hoy |
| `volver` | Volver | footer popup (HIS no dice Cancelar) |
| `cerrar` | Cerrar | turnos hoy |
| `informacion` | Información | header turnos hoy |
| `el_paciente_tiene_turnos_pactados_para_este_dia` | (Resources) | turnos hoy |
| `desea_continuar` | ¿Desea continuar? | turnos hoy |
| `DEBE_SELECCIONAR_UN_PACIENTE` | Debe seleccionar un paciente. | toast |
| `REQUIRED_PRESTACION` | Se debe seleccionar una prestación. | toast |
| `REQUIRED_CENTRO` / centro | (T5 ya) | toast |
| servicio requerido | Debe elegir el Servicio. | toast API T5 |
| `FECHA_MENOR_ACTUAL` | La fecha no puede ser menor a la fecha actual | toast |
| `HORA_INVALIDA_ERROR` | La hora debe ser posterior a la hora actual. | toast |
| `NO_SE_PUEDE_ASIGNAR_SOBRETURNO` | No se puede asignar un sobreturno sin grilla generada para el dia seleccionado. | toast |
| `agenda` | Agenda | accordion Turnero (ítem actual, highlight) |
| `turnero` | Turnero | tab accordion |
| `paciente` | Paciente | tab accordion (además readonly popup) |
| `acciones` | Acciones | south accordion |
| `datos_paciente` | Datos del Paciente | tab Paciente disabled |
| `grupo_familiar` | Grupo Familiar | disabled |
| `historial_convenio` | Historial Convenio | disabled |
| `direccion_mail_te` | Dirección Correo Teléfono | disabled |
| `documentos` | Documentos | disabled |
| `consulta_de_turnos` | Consulta de Turnos | disabled |
| `atenciones_anteriores` | Atenciones Anteriores | disabled |
| `prescripciones` | Prescripciones | disabled |
| `estado_estudios` | Estado Estudios | disabled |
| `recetas_web` | Recetas Web | disabled |
| `pedidos_receta_web` | Pedidos Receta Web | disabled |
| `turnos_multiples` | Turnos Múltiples | Turnero disabled |
| `consulta_agenda` | Consulta Agenda | Turnero disabled |
| `avisos` | Avisos | Turnero disabled HIS |
| `reasignacion_turnos` | Reasignación de Turnos | Turnero disabled |
| `pre_agenda_turnos` | Pre Agenda Turnos | Turnero disabled |
| `historial_turno` | Historial Turnos | Turnero disabled |
| `fecha` | Fecha | popup (si no estaba) |
