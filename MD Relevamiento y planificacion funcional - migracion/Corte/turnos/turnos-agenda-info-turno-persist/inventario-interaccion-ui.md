---
title: Inventario interacción — persist infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-persist.interaccion
---

# Inventario interacción

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Textarea observaciones | modo otorga | bind `turnoInfo.observaciones` | ya ngModel; **POST** este slice |
| Calendar fecha | modo otorga | bind `fechaPrescripcion`; max hoy | `type=date` o paridad `dd/MM/yy` + botón |
| Asignar Turno | footer | valida → (popup confirma) → otorga + persist | mismo testid |
| `$popUpConfirmaTurnoPrescripcion` | BB si req y (vacía o vencida al recepcionar) | modal sin X | **nuevo** |
| Aceptar confirma | | sigue otorga | |
| Cancelar confirma | | vuelve a infoTurno; no otorga | |
