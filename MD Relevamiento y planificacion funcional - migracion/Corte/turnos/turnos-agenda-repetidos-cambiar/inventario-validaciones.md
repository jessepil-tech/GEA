---
title: Inventario validaciones — cambiar horario repetidos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.validaciones
---

# Inventario validaciones

Fuente: `BBTurnosRepetidos` L192–323 · T5 reserva/libera.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Grilla vacía | `no_se_encontraron_registros` | emptyMessage tabla | GET `LIBRES` [] |
| Reserva 4xx | MessageManager | toast sin `/500` | POST `…/reservar` |
| Libera anterior 4xx (Camino 1) | HIS no libera | toast; lista ya con el nuevo | POST `…/liberar` |
| Motivo libera anterior | n/a (desviación) | — | `idMotivoLiberacion` **91004** (mismo Volver T5.3) |
| Click fila mientras POST | — | no doble submit | — |
| Cancelar / X | — | no escribe | — |

Reusa T5: lock, paciente/convenio/prestación del north. Calendario otro día **WAIVE**.
