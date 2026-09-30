---
title: Copy — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-hab-equipo.copy
---

# Copy

Fuente: `HOSPITAL_2/.../Resources.properties` y `MessageBundle`.  
La hoja de profesional ya tiene el mismo bloque de vigencia en `hab-turnos-labels.ts`. Este corte suma el título de equipo y `buscar_equipo`. El popup usa **Cerrar**, no Cancelar.

| msg.key | Valor | Dónde |
|---------|-------|-------|
| `hab_turnos_equipo` | Habilitación de Turnos por Equipo | título, accordion, header popup |
| `equipo` | Equipo | north, columna buscador, header popup |
| `buscar` | Buscar | botón north y lupa del buscador |
| `buscar_equipo` | Buscar Equipo | header dialog 1200×550 |
| `centro_atencion` | Centro Atención | north (disabled) y filtro buscador |
| `servicio` | Servicio | north (disabled) y filtro/columna buscador |
| `centro` | Centro | columna buscador |
| `fecha_vigencia` | Fecha Vigencia | columna y popup (*) |
| `fecha_fin_vigencia` | Fecha Fin Vigencia | columna y popup |
| `envia_mail_turno` | Enviar Correo Turno | columna (disabled) y popup |
| `envia_sms_turno` | Enviar Sms Turno | columna (disabled) y popup |
| `envia_mail_recordar_turno` | Enviar Correo Recordar Turno | columna (disabled) y popup |
| `envia_mail_turno_no_asistido` | Enviar Correo Turno No Asistido | columna (disabled) y popup |
| `acciones` | Acciones | columna |
| `editar` | Editar | title lápiz |
| `eliminar` | Eliminar | title tacho |
| `agregar` | Agregar | south, ancho 135px |
| `ctd_hs_previa_recordar_tur` | Cantidad Horas Previa Para Recordar Turno | popup (*) |
| `porc_max_sobreturno_dia` | Porcentaje Máximo Sobreturnos Diarios | popup (*) |
| `ctd_hs_min_dar_turno_pre` | Cantidad Horas Minimas Para Dar Turno Presencial | popup (*) |
| `ctd_hs_min_dar_turno_te` | Cantidad Horas Minimas Para Dar Turno Telefónico | popup (*) |
| `ctd_hs_min_dar_turno_web` | Cantidad Horas Minimas Para Dar Turno Web | popup (*) |
| `ctd_dias_visualiza_turno_pre` | Cantidad Días Visualiza Turno Presencial | popup (*) |
| `ctd_dias_visualiza_turno_te` | Cantidad Días Visualiza Turno Telefónico | popup (*) |
| `ctd_dias_visualiza_turno_web` | Cantidad Días Visualiza Turno Web | popup (*) |
| `req_ctrl_ctd_max_dia` | Requiere Control Cantidad Máxima de Turnos por Día | popup |
| `ctd_max_tur_dia` | Cantidad Máxima de Turnos por Día | popup (*) |
| `ctd_max_sobreturno_x_dia` | Cantidad Máxima Sobreturno por Día | popup (*) |
| `permite_dar_turno_pers_ate_amb` | Permite Dar Turnos al Personal Desde Atención | popup |
| `leyenda` | Los campos marcados con (*) son obligatorios | popup |
| `aceptar` | Aceptar | popup y buscador múltiple |
| `cerrar` | Cerrar | popup (immediate) |
| `volver` | Volver | footer buscador |
| `desea_eliminar_entidad` | ¿Desea eliminar la Entidad? | confirm del tacho |
| `no_se_encontraron_registros` | No se encontraron registros | tabla buscador |
