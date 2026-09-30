---
title: Inventario validaciones — cambiar horario turnos múltiples
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.validaciones
---

# Inventario validaciones

Fuente: `BBAgenda.actBtnCambiarTurnoMultiple` L2639–2694 · `actionBtnReemplazarTurnoMultiple` L2697–2743.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Grilla vacía | `no_se_encontraron_registros` | emptyMessage tabla | GET `LIBRES` [] |
| Filtro no resuelto (`id_motivo_suspende` ≠ `id_objeto`) | `Error` | toast | — (Web: keys de la fila) |
| `cantidadMaxTurnoExcedida` personal/equipo/servicio/convenio/plan | MessageBundle `LIMITE_TURNO_*` | toast; no pisa | campos GET T5 |
| `modificable` false | `El Turno no pudo ser otorgado.` | toast; no pisa | GET T5 |
| ini &lt; sysdate | `La hora debe ser posterior a la hora actual.` | toast; no pisa | — |
| GET T5 4xx | — | toast sin `/500` | GET `…/grilla` |
| Click fila mientras carga | — | no doble swap | — |
| Cancelar / X | — | no pisa | — |

Reusa T5: paciente/convenio del north. Equipo en el GET **diferido** D-TUR-17.  
Recorte ventana vecinos **WAIVE** (código muerto; HIS consulta `00:00`–`23:59`).
