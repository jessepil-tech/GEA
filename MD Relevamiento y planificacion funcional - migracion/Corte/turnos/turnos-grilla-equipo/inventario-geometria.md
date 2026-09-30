---
title: Inventario geometría — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo.geometria
---

# Geometría — delta

No hay hoja nueva. El radio Equipo ya ocupa la tercera opción, al lado de Servicio y Profesional, en generar, eliminar y consulta.

| Control | Legacy | Destino |
|---------|--------|---------|
| Input equipo | `styleClass="InputWid100"` | el mismo ancho que el de personal, hoy disabled |
| Diálogo buscar | `header` Buscar Equipo, no maximizable | el diálogo ya usado en la hab |
| Combo grupo | fila `grp_prest_equipo`, visible con el radio Equipo | misma fila que el grupo de personal |

No se mueven columnas de la grilla ni el calendario.
