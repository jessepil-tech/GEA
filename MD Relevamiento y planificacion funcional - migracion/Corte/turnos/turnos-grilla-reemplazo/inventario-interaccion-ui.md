---
title: Inventario interacción — T6.5 reemplazo profesional
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.interaccion
---

# Inventario interacción — Reemplazo profesional

| Control | HIS | Este corte |
|---------|-----|------------|
| Buscar profesional origen | input + lupa → `buscadorPersonalServicio` (`atencionTurno=true`) | reuso T2/T4 · reabrir con **apellido+nombre** del elegido (no el label compuesto) |
| Elegir origen | llena nombre + centro + servicio disabled | igual |
| Volver/Cancelar buscador | `setPersonalServicioBuscado(null)` | vacía origen o reemplazante según el kind (canon v1.17) |
| Buscar reemplazante | mismo dialog; exige origen; filtra `idServicio` | igual |
| Motivo | combo north `REEMPLAZO_TURNO` | GET T1 |
| Días / feriado | checks (todos en true salvo feriado) | igual |
| Consultar | llena tabla `TmpTurno` | GET JDBC |
| Click header col | `p:column sortBy` | igual T5 (`ordenar` / ▲▼) |
| Footer leyenda estilos | reservado / sobreturno / reemplazado / suspendido / inhibido | anclada v1.19 |
| Seleccionar todo | check header | igual |
| Reemplazar Agenda | `confirm` → SP | modal DS → POST ids |
| Quitar Reemplazo | `confirm` → SP nulls | modal DS → POST ids |
| Volver | `inicio.faces` config | menú T4 |
| Equipo | no hay | N/A |
