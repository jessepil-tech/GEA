---
title: Interacción — Turnos por equipo
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.interaccion
---

# Interacción

| Acto | Legado | Este corte |
|------|--------|------------|
| Buscar equipo | `onBuscarEquipoServCentro`. Un solo hit selecciona y no abre el diálogo | se abre siempre el diálogo, igual que la hab firmada. El texto busca en equipo, centro o servicio. No reenvía el centro ni el servicio ya elegidos |
| Volver del buscador | `actionBtnVolver` llama `setEquipoServCentroBuscado(null)` y vacía la elección | igual: cancelar suelta el equipo |
| Sin equipo elegido | ítems del menú lateral disabled | igual |
| Datos | equipo, centro, servicio y observaciones disabled | solo lectura |
| Grupo | grilla `rows="8"`, alta en el pie, lápiz y tacho por fila. Confirm `desea_eliminar_prestacion` | se porta |
| Prestaciones | combo de grupo, grilla, alta con buscador multi-select, lápiz y tacho. Confirm `desea_eliminar_grupo_prestacion` | se porta. El `filterBy` de la columna prestación no se porta, igual que T3 |
| Horario | combo de grupo, combo de vigencia, nueva vigencia, editar, grilla de días | se porta |
| Tacho del día | solo si la vigencia es posterior a hoy | se porta |
| Reserva del día | checkbox. Apagado: inicio, fin, cant. mín. y cant. hs. en cero y disabled | se porta. Solo esta hoja |
| Lápiz del día | abre el popup de convenio. Disabled si la reserva está apagada | se porta. Apagado se ve con opacidad 0.4 |
| Popup convenio | lista `rows="3"`, criterio + Buscar, plan, Agregar, Aceptar | se porta. Plan gris hasta el convenio. Agregar apagado hasta convenio y plan. Tipear el criterio suelta el id. Confirm `desea_eliminar_el_registro` |
| Horario especial, inhibición, ocupación | ítems del mismo menú | diferidos. Quedan visibles y disabled |
