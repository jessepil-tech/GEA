---
title: Inventario validaciones — turnos repetidos
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-repetidos.validaciones
---

# Inventario validaciones

Fuente: `agenda.xhtml` L285 · `BBTurnosRepetidos` L120–135.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Menú: `!modificable` | disabled | menú | — |
| Menú: sin paciente y no LIBRE | disabled | menú | — |
| Menú: slot con otro paciente ≠ west | disabled | menú | — |
| Prestación/paciente/convenio de north | HIS copia al slot | disabled inputs | POST body |
| 0 slots en días | No se encontraron turnos en los días seleccionados | popup Información | result |
| Parcial | Se generaron {%1} turnos, {%2} no se encontraron. | popup | result |
| Asignar Turnos sin reservados | botón disabled | south | — |
| POST error | — | toast sin `/500` | 4xx |
| Ctd. Turnos ≤ 0 | (Api `Ctd. Turnos`) | inline + botón disabled + toast `Ctd. Turnos debe ser mayor a 0.` | `ctdTurnos() <= 0` |

Reusa T5: lock, pagar, saldo, fecha prescripción al otorgar cada fila.
