---
title: Inventario interacción — cambiar horario repetidos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.interaccion
---

# Inventario interacción

Fuente: `turnosRepetidos.xhtml` L161–183 · L311–382 · `BBTurnosRepetidos`.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Icono ⇄ reservados | `commandButton` col acciones width 50 | `actBtnCambiarTurno` + popup | emit `cambiarReservado` |
| Icono ⇄ observaciones | col width 60 | `actBtnCambiarTurnoNoEncontrado` | emit `cambiarObs` |
| `$popupCambiarTurno` | visible + update | modal 1200×550, **closable=true** | `app-ds-dialog` X + Cancelar |
| `p:dataTable` rowSelect | click fila | `actionBtnReemplazarTurno` (reserva) | click fila → POST reservar |
| Cancelar | footer MarAuto (solo ese botón) | hide dialog | `dsDialogFooter` Cancelar al final |
| Calendario / mes | **comentado** | — | no render (**WAIVE**) |

CTA = click de fila (no botón Aceptar). Look Origin; geometría HIS.
