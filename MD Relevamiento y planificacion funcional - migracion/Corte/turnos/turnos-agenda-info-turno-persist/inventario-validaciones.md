---
title: Inventario validaciones — persist infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.validaciones
---

# Inventario validaciones

Fuente: `BBAsignacionTurnos.actionBtnOtorgarTurno` L948–992.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Fecha > hoy | La fecha de prescripción seleccionada no puede ser mayor a la actual. | toast | create otorgar |
| `req_fecha_prescrip_amb='S'` y fecha null | Debe ingresar la fecha de prescripción. | toast | create otorgar |
| req + `req_ctrl_fecha_prescrip` y fecha < hoy−`ctd_max_dias_prescrip` | La fecha de prescripción seleccionada supera la cantidad máxima de días… | toast | create otorgar |
| req y (vacía o vencida al recepcionar) | popup Confirmación (no toast) | modal | el POST no corre hasta Aceptar |
| Obs > 250 | truncar HIS / columna | truncar | truncar |

Reusa T5: lock, pagar, saldo, reasignar obs — **antes** de este persist.
