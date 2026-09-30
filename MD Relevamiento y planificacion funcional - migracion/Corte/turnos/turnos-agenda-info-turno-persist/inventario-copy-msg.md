---
title: Inventario copy — persist infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.copy
---

# Inventario copy

Chrome labels: reusa T5.1e (`fechaPrescripcion`, `observaciones`).

| msg.key / MessageBundle | Valor | Constante | Este slice |
|-------------------------|-------|-----------|------------|
| `confirmacion` | Confirmación | `confirmacion` | header popup |
| `prescripcion_vencida` | La prescripción estará vencida al momento de recepcionar el turno. | `prescripcionVencida` | popup si hay fecha |
| `no_ha_ingresado_fecha_prescripcion` | No ha ingresado una fecha prescripción. | `noHaIngresadoFechaPrescripcion` | popup si vacía |
| `desea_continuar` | ¿Desea continuar? | `deseaContinuar` | ya T5.1d |
| `aceptar` / `cancelar` | Aceptar / Cancelar | ya | |
| `FECHA_PRESCRIPCION_POSTERIOR_ACTUAL` | La fecha de prescripción seleccionada no puede ser mayor a la actual. | toast | |
| `FECHA_PRESCRIPCION_REQUIRED` | Debe ingresar la fecha de prescripción. | toast | |
| `FECHA_PRESCRIPCION_SUPERA_CANTIDAD_DIAS` | La fecha de prescripción seleccionada supera la cantidad máxima de días de prescripción para este plan. | toast | |
