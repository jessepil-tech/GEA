---
title: Inventario interacción — T6.2 cola reasignar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.interaccion
---

# Inventario interacción — Cola Reasignación de Turnos

Fuente: `turnosAReasignar.xhtml` · `BBTurnosAReasignar`.

| Control | Disparador | Efecto HIS | Web (Camino 1) |
|---------|------------|------------|----------------|
| Accordion Reasignación | menuitem live | include hoja | **enable**; quitar tooltip T6 padre |
| Combos centro/servicio | ajax `actChangeConfiguracionTurno` | recarga combos + sesión | recargar dependientes |
| Equipo | combo | live HIS | **disabled** D-TUR-17 |
| Profesional + lupa | `buscarPersonal` | dialog buscador T5 | reusar buscador T5 |
| Fechas / horas | ajax change | sesión | north |
| Consultar | `actBtnConsultar` | `listTurnos` | GET; vacío = emptyMessage |
| Limpiar | `actBtnLimpiarDatos` | resetea north | igual |
| Gear REASIGNAR TURNO | `actBtnIrAGrilla` | sesión + redirect `agenda.faces` | hoja Agenda + sesión T6.1 `turnoAReasignar=true` (**sin** overlay pendiente) |
| Gear INFORMACIÓN | `actionBtnReasignarTurno` | `popupInfoPacienteTurno` | reusar T5.1 |
| Botón observaciones | `actionBtnAgregarObservaciones` | dialog 700px | igual |
| Aceptar obs | UPDATE + toast + reconsultar | persist PK cola | PATCH + toast HIS |
| Cancelar obs | hide + reconsultar | no persiste | igual |
| Imprimir / Excel | south | acto HIS | **disabled** + tooltip hijo |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Overlay `PENDIENTE_LIBERAR` al venir de la cola | Prohibido — HIS `turno.setTurnoAReasignar(true)` |
| Seed de `turno_a_reasignar` | Prohibido — T4 eliminar |
| Reabrir T6.1 / reimplementar otorga+libera | Prohibido |
| Fork de ruta | Prohibido |
| Auto-consultar al abrir sin clic | HIS constructor **no** llama Consultar — lista vacía hasta clic |
