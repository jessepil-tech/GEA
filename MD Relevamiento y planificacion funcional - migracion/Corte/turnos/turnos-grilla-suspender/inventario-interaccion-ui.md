---
title: Inventario interacción — T6.4 suspender grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.interaccion
---

# Inventario interacción — Suspender / quitar suspensión

| Control | HIS | Este corte |
|---------|-----|------------|
| Radio TipoFiltro | serv / pers / equipo | serv+pers live; **equipo disabled** |
| Change centro/servicio | recarga combos | igual T4 |
| Buscar profesional | `buscadorPersonalServicio` | reuso T2/T4 |
| Buscar equipo | `buscadorEquipoServCentro` | **no** (D-TUR-17) |
| Consultar | llena tabla `TmpTurno` | GET JDBC |
| Click header col | `p:column sortBy` | igual T5 (`ordenar` / ▲▼) |
| Footer leyenda estilos | sobreturno / cancelado / reemplazado / inhibido | igual |
| Seleccionar todo | check header | igual |
| Suspender | abre popup motivo | igual |
| Aceptar motivo | llama SP con seleccionados | POST ids |
| Cancelar popup | cierra, no escribe | igual |
| Quitar suspensión | SP sin motivo | POST ids |
| Volver | menú | igual |
