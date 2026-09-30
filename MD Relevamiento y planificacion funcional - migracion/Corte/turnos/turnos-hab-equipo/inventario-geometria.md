---
title: Geometría — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-hab-equipo.geometria
---

# Geometría

Xhtml: `habTurnosEquipoServ.xhtml`. Misma cáscara que hab profesional, con Equipo en lugar de Profesional.

| Zona | Contrato |
|------|----------|
| North | `panelGrid columns="4"`. Equipo + input **300px** + Buscar **80px** + spacer. Centro y Servicio en inputs **300px** disabled, misma fila de pares |
| West | accordion con un ítem, el título de la hab |
| Grilla | fecha y fin **150px**; cuatro flags **70px** centrados; acciones **10%** |
| South | Agregar **135px**, centrado (`MarAuto`) |
| Dialog buscar | **1200 × 550** (alto = cuerpo). Header Buscar Equipo |
| Popup hab | **1000 × 400**. Tabla 100%. Calendarios en celdas **200px**. Numéricos `InputWid100` alineados a la derecha |
| Leyenda | debajo de la tabla del popup, antes de Aceptar / Cerrar |

Disabled = `#dadada`. Checks de la grilla se ven aunque estén disabled.
