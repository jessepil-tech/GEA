---
title: Inventario interacción — T6.3 historial turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.interaccion
---

# Inventario interacción — Historial Turnos

Fuente: `historialTurno.xhtml` · `BBHistorialTurno`.

| Control | Disparador | Efecto HIS | Web (Camino 1) |
|---------|------------|------------|----------------|
| Accordion Historial | menuitem live | include hoja | **enable**; quitar tooltip T6 padre |
| Combos centro/servicio | ajax `actChangeConfiguracionTurno` | recarga combos; limpia personal si hay equipo | recargar dependientes |
| Equipo | combo live HIS | recarga | **disabled** D-TUR-17 |
| Profesional + lupa | `buscarPersonal` | dialog buscador T5 | reusar buscador T5 |
| Fechas / horas | inputs | sesión | north; no auto-GET |
| Consultar | `actBtnConsultar` | `listTurnos` sort `fechaHoraTurIni` asc | GET; vacío = emptyMessage |
| Ícono info (col 24) | `infoHistTurno(turno)` · `update=:popupInfoHistTurno` | `displayInfoHistTurno=true` | dialog 1200×512 |
| Volver dialog | `actBtnVolverInfoHistTurno` | `hideDialogs` (deja display=true) | cierra |
| Excel | `actBtnExportarExcel` | XLSParser sobre `listTurnos` | **disabled** + tooltip hijo |
| Leyenda south | estático | 4 clases CSS | igual |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Auto-consultar al abrir | HIS constructor **no** llama Consultar |
| Filtrar SELECT por call center | Prohibido |
| Toast `_3` fechas | Prohibido |
| Seed de `hist_turno` | Prohibido — T5 otorga/libera |
| Fork de ruta | Prohibido |
| Limpiar Datos | HIS **no tiene** ese botón en esta hoja |
| D-TUR-66 fila Consultar centrada | No: HIS ya trae botón con copy en la fila de horas |
