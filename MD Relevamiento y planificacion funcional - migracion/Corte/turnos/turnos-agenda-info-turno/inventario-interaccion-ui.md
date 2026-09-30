---
title: Inventario interacción — T5.1e infoTurno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno.interaccion
---

# Inventario interacción — infoTurno

Fuente: `asignacionTurnos.xhtml` L274–278 · `infoTurno.xhtml`.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Dialog | reserva / sobreturno Aceptar / menú Información | abre 1200×520 | `turnos-info-turno-dialog` |
| X chrome | solo `!otorgaTurno` | cierra | `turnos-info-turno-x` hidden si `modoOtorgar` |
| Asignar Turno | footer 120px si otorga | `actionBtnOtorgarTurno` | `turnos-info-turno-otorgar` |
| Cancelar | footer si otorga | libera RESERVADO | `turnos-info-turno-cancelar` |
| Volver | footer si solo lectura | cierra | mismo cancelar, label Cerrar (T5) |
| Poll 59 s | otorga | tomar lock | ya T5 |
| Tabla repetidos/múltiples | `otorgaTurnoRepetido/Multiple` | **diferido** | no render |
| Imprimir prep | solo `UserSession.recepcion` | T7 | **N/A** agenda |
