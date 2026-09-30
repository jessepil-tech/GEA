---
title: Geometría — Turnos por equipo
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.geometria
---

# Geometría

Fuente: `turnosEquipo.xhtml`, `datosTurnosEquipo.xhtml`, `grupoPrestacionesTurnoEquipo.xhtml`, `prestacionGrupoPrestacionesTurnoEquipo.xhtml`, `horarioTurnoEquipo.xhtml`.  
La hoja reusa el chrome de T3. Estos son los anchos del xhtml.

| Zona | Contrato |
|------|----------|
| Norte | `panelGrid columns="4"`. Equipo **258px**, margen 10px. Buscar con margen 10px. Centro **258px** disabled. Servicio **300px** disabled |
| Dialog buscar equipo | **1200 × 550**. Header Buscar Equipo. El mismo diálogo que la hab |
| Datos | Equipo, centro y servicio **500px** disabled. Observaciones **100%** disabled |
| Grupo | grilla `rows="8"`. Nombre al **95%**. Acciones **80px** |
| Prestaciones | combo de grupo **200px**. Grilla `rows="6"`. Código, web, call center y activa **80px**. Acciones **80px** |
| Horario | combos de grupo y vigencia **200px**. Nueva vigencia y Editar con margen 10px. Días `rows="8"` |
| Cabecera de días | Disponibilidad Horaria **360px** (día, hora desde, hora hasta). Reserva de turno **300px** (reserva, inicio/fin, cant. mín., cant. hs., ctd. planes) |
| Columnas del día | hora desde y hora hasta **120px**. Reserva **50px**. Inicio/fin, cant. mín., cant. hs. y ctd. planes **120px**. Acciones **60px** |
| Popup convenio | **700 × 250**. Lista `rows="3"`. Buscador de convenio **1200 × 550** |
