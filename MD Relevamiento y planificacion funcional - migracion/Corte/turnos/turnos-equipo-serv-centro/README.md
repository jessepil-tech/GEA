---
title: Diferido — vínculo equipo-servicio-centro
description: Padre de la hab de turnos por equipo. Tab datosEquipoServCentro. 0 filas hoy.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-equipo-serv-centro
---

# Vínculo equipo-servicio-centro (`turnos-equipo-serv-centro`)

**Estado:** `diferido(fixture)` abierto 2026-09-22 desde [`turnos-hab-equipo/`](../turnos-hab-equipo/).

`ts.equipo_serv_centro` existe (Flyway V56) y no tiene ABM. Para probar la hab hay una fila mock `EQDEMO1001` (centro 1001, servicio 10). El ABM del vínculo sigue diferido.

Semilla cuando se cobre: `datosEquipoServCentro` (tab de `servicioCentro.xhtml`, no el menú TURNOS). Índice 2026-09-22: 3 xhtml / 3 beans / 0 firmas / 0 reportes.

**No es** el ABM de `item_equipo` (menú equipo 10282). **No es** la hab (D-TUR-12).
