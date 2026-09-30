---
title: Validaciones — Turnos por equipo
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.validaciones
---

# Validaciones

Misma familia que T3: `TurnosHorariosValidation` y `horarios-turnos-validation.ts`.  
El bean de equipo no agrega mensajes propios fuera de esa familia, salvo el botón del convenio.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Campos (*) vacíos | Debe completar todos los campos requeridos. | sí | sí, nombre de grupo |
| Prestación | Se debe seleccionar una prestación. | sí | sí |
| Fecha de vigencia | Debe ingresar la fecha de vigencia. | sí | sí |
| Fin no posterior a la vigencia | La fecha de fin de vigencia debe ser posterior. | sí | sí |
| Vigencias solapadas | Existen fechas solapadas. | sí, toast | sí. POST de la misma vigencia → 400 |
| Copiar vigencia anterior | checkbox del alta | sí | sí, `copiarValoresVigentes` |
| Hora desde | Debe ingresar hora y minutos Desde | sí | sí |
| Hora hasta no posterior | El horario hasta debe ser posterior. | sí | sí |
| Horas solapadas el mismo día | Existen horarios solapados. | sí | sí |
| Reserva prendida | cant. mín., inicio/fin y cant. hs. | sí, los tres textos de T3 | sí. Apagada: los tres van en cero |
| Convenio sin plan | el plus del xhtml solo mira `idConvenio` | Agregar apagado hasta convenio y plan | el handler sigue rechazando el pedido incompleto. Ese texto no se publica |
