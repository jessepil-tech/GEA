---
title: Inventario interacción — turnos repetidos
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-repetidos.interaccion
---

# Inventario interacción

Fuente: `agenda.xhtml` L285 · `turnosRepetidos.xhtml` · `BBAgenda` L2446 · `BBTurnosRepetidos`.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Menú TURNOS REPETIDOS | gear fila | sesión TurnoRepetido + navega faces | mismo `turno-menu-*`; **no** botón inline |
| `$popupTurnosRepetidos` | al entrar (visible=true) | modal sin X | nuevo |
| Asignar Turno (popup) | footer 105px | **reserva** N | no confundir con otorga T5 |
| Volver (popup) | | cierra; no reserva | |
| `$popupMensajeErrorTurnosRepetidos` | 0 o parcial | Aceptar | |
| Asignar Turnos (south) | 110px si hay reservados | infoTurno tabla | |
| Volver (south) | | libera N + grilla | |
| Icono cambiar horario | col acciones | **done** T5.3-b | `turnos-repetidos-cambiar-dialog` |
