---
title: Inventario validaciones — T5.1e infoTurno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno.validaciones
---

# Inventario validaciones — infoTurno chrome

No hay validaciones **nuevas** de este corte. Reusa T5 / T5.1d:

| Regla | Legacy | Este slice |
|-------|--------|------------|
| Lock otro operador | toast T5 | igual |
| Pagar / saldo | popups T5.1d | igual (antes de otorgar) |
| Fecha prescripción requerida / vencida | `FECHA_PRESCRIPCION_*` | **diferido** cobros (chrome date; no bloquea) |
| Cancelar otorga | libera RESERVADO (DELETE si sobreturno) | igual T5/T5.2 |
