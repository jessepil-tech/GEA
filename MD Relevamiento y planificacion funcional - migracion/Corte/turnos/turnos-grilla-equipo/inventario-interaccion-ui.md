---
title: Inventario interacción — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo.interaccion
---

# Interacción — radio Equipo

Hoy el radio está en las tres pantallas y no se puede elegir.

| Acto | Legacy | Este corte |
|------|--------|------------|
| Elegir Equipo | `actChangeTipoFiltro` limpia centro, servicio, personal y equipo | encender el radio; al elegirlo, limpiar lo otro |
| Buscar | abre `buscadorEquipoServCentro` | el mismo diálogo de la hab (siempre abre; un hit no auto-selecciona) |
| Grupo | combo `grp_prest_equipo`, vacío hasta elegir equipo | cargar los grupos de D-TUR-13 |
| Generar | `REQUIRED_EQUIPO` si no hay equipo | el botón usa el equipo elegido |
| Eliminar | mismo radio y el mismo error | igual |
| Consultar | el calendario filtra por equipo y por su grupo | igual |

El buscador de personal y el camino serv/pers quedan como están.
