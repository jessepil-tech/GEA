---
title: Inventario validaciones — T6.3 historial turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.validaciones
---

# Inventario validaciones — Historial Turnos

Fuente: `BBHistorialTurno.actBtnConsultar` L159–176.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| `horaDesde.after(horaHasta)` | `WRONG_INTERVAL_HOUR` WARN | toast | 400 mismo texto |
| Fechas invertidas | **no valida** | no toast `_3` | no inventar 400 de fechas |
| Lista vacía | emptyMessage tabla | copy HIS; **no** toast | 200 lista `[]` |
| Call center | **no** entra al SELECT hist; sí a combos | sesión T5 en combos | combos T5; GET hist sin `idCallCenter` en WHERE |
| Actor sin menú Agenda | no entra | no ruta | 401/403 sin `/500` |

No hay `Raise_application_error` de rol funcional.  
HIS **no** exige fechas ≥ hoy. Defaults constructor: sysdate / sysdate · 00:00 / 23:59.
