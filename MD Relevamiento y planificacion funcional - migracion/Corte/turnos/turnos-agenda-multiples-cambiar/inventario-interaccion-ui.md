---
title: Inventario interacción — cambiar horario turnos múltiples
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.interaccion
---

# Inventario interacción

Fuente: `turnosMultiples.xhtml` L301–303 · L540–575 · `BBAgenda`.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Icono ⇄ slots | `commandButton` col width 24 | `actBtnCambiarTurnoMultiple` + popup | emit `cambiarSlot` (quitar `disabled`) |
| `$popupCambiarTurno` | visible + update | modal 1200×550, **closable=true** | `app-ds-dialog` X + Cancelar |
| `p:dataTable` rowSelect | click fila | `actionBtnReemplazarTurnoMultiple` (swap memoria) | click fila → pisa slot; **no POST** |
| Cancelar | footer MarAuto (solo ese botón) | hide dialog | `dsDialogFooter` Cancelar al final |

CTA = click de fila (no botón Aceptar). Look Origin; geometría HIS.  
No calendario en este popup (a diferencia del de repetidos, que está comentado).
