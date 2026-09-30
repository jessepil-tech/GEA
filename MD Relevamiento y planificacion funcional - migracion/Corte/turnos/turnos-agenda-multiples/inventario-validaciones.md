---
title: Inventario validaciones — turnos múltiples
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-multiples.validaciones
---

# Inventario validaciones

Fuente: `BBAgenda.actBtnAceptarTurnosMultiples` · `actBtnConsultarTurnosMultiples` · `actionBtnReservarTurnosMultiples` · SP reserva.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Agregar sin prestación | Debe seleccionar al menos una Prestación. | toast | — |
| Prestación `420101` sin servicio | Debe elegir el Servicio. | toast | — |
| Consultar sin convenio | Debe seleccionar un Convenio | toast | 4xx |
| Asignar tabla vacía | No hay turnos | botón disabled + toast | — |
| Popup centro sin valor | Debe elegir el Centro de Atención. | toast | — |
| Mismo horario en el lote | El paciente tiene turnos para el mismo horario. | toast | 4xx |
| Slot ocupado | El turno que desea otorgar ha sido ocupado | toast | 4xx T5 |
| POST error | — | toast sin `/500` | 4xx |

Reusa T5 al otorgar: lock, pagar, saldo, fecha prescripción.
