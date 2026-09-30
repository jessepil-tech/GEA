---
title: Inventario validaciones — T6.2 cola reasignar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.validaciones
---

# Inventario validaciones — Cola Reasignación de Turnos

Fuente: `BBTurnosAReasignar.actBtnConsultar` ~197–211.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| `fechaDesde.after(fechaHasta)` | `WRONG_INTERVAL_DATE_3` WARN | toast | 400 mismo texto |
| `horaDesde.after(horaHasta)` | `WRONG_INTERVAL_HOUR` WARN | toast | 400 mismo texto |
| Lista vacía | emptyMessage tabla | copy HIS; **no** toast | 200 lista `[]` |
| Call center | filtro `id_call_center` en la función | sesión T5 | T5 |
| Obs Aceptar | UPDATE por `idTurno` = PK cola | toast éxito | 204/200 + id |
| Obs Cancelar | no UPDATE | cierra | no llama |
| Actor sin menú Agenda | no entra | no ruta | 401/403 sin `/500` |

No hay `Raise_application_error` de rol funcional en este bean.  
HIS **no** exige fechas ≥ hoy (a diferencia de Consulta Agenda `_9`).
