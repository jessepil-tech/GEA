---
title: Validaciones — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-hab-equipo.validaciones
---

# Validaciones

Fuente: `BBHabTurnosEquipoServ` + JSF del popup. Mismos textos en UI y API. Alta y edición.

| Caso | Mensaje | Cuándo |
|------|---------|--------|
| Sin equipo elegido | Debe seleccionar un servicio centro. | Aceptar con `idServicio` null (`REQUIRED_SERVICIO_CENTRO`) |
| Sin fecha vigencia | Debe ingresar la fecha de vigencia. | `REQUIRED_FECHA_VIGENCIA` |
| Fin ≤ inicio | La fecha de fin de vigencia debe ser posterior a la fecha de vigencia | `WRONG_INTERVAL_VIGENCIA` |
| Entero &lt; 0 | Debe ingresar un valor entero superior a 0. | `LongRangeValidator.MINIMUM` detail, minimum 0 |
| Porcentaje fuera de 0–100 | Debe ingresar un valor entero entre 0 y 100. | `DoubleRangeValidator.NOT_IN_RANGE` |
| Tope diario apagado | inputs `ctd_max_tur_dia` y `ctd_max_sobreturno_x_dia` disabled | `req_ctrl_ctd_max_dia` en false |
| Fecha vigencia en edición | calendar disabled | `addServicio` false |
| Agregar | botón disabled | north sin `idServicio` |
| Alta ok | Registro insertado con éxito. | `ROW_INSERT_INFO` |
| Edición ok | Registro actualizado con éxito. | `ROW_UPDATE_INFO` |
| Baja ok | Registro eliminado con éxito. | `ROW_DELETE_INFO` |
| Baja | ¿Desea eliminar la Entidad? | confirm; cancelar no llama al bean |

Mínimo de calendario: `fechaActual` en vigencia y fin.
