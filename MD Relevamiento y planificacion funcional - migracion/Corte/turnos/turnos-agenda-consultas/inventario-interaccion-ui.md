---
title: Inventario interacción — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-consultas.interaccion
---

# Inventario interacción UI — Consulta Agenda (G0)

Fuente: `asignacionTurnos.xhtml` L79–81 · `consulta.xhtml` · `BBConsultaAgenda`.

Layout HIS hoja: **[accordion 170] [north filtros | tabla fill | south 120px]**. **Sin** calendario 260.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Turnero Consulta Agenda | `actionSelectMenu('Consulta', consulta.faces)` | cambia hoja | `vista=consulta`; highlight; oculta calendario Agenda |
| Turnero Agenda | agenda.faces | vuelve | `vista=agenda` |
| Turnero Sobreturno | disabled si no Agenda | — | **disabled** en esta vista (HIS L74) |
| Centro / servicio combo | `actChangeConfiguracionTurno` | recarga combos | GET combos T5; equipo sigue disabled |
| Profesional input+lupa | change / click `buscarPersonal` | popup personal | tipeable + `HabBuscadorDialog` personal-servicio |
| Equipo combo | change | HIS recarga | **visible disabled** D-TUR-17 |
| Convenio input+lupa | change / click | popup convenio | tipeable + `ConvenioBuscadorDialog` T5.1 |
| Estado | — | filtro Consultar | Todos / Libre / Otorgado |
| Fecha desde/hasta | calendar mindate hoy | default hoy | `input type=date` min hoy |
| Hora desde/hasta | timePicker 65px | default 00:00–23:59 | `app-grilla-time-picker` |
| Incluye sobreturnos | check default true | filtro | checkbox |
| Consultar | `actBtnConsultar` | llena `listTurnos` | POST/GET query; no escribe |
| Col info | `actBtnInfoTurno` | `$popupInfoTurno` | reusa `turnos-info-turno-dialog` |
| Imprimir | `actBtnImprimir` → `ConsultaAgenda.rptdesign` | PDF viewer | **live** GET Api → sidecar `reportId=ConsultaAgenda` (cable). **Filas/valores** hijo [`turnos-agenda-consultas-pdf`](../turnos-agenda-consultas-pdf/) **gate-done** |
| Excel | `generarReporteExcel` POI | xls | **visible disabled** `diferido(turnos-agenda-consultas-export)` |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Fork `/turnos/consulta-agenda` | Prohibido |
| Confundir con T4 `/configuracion/grilla-turnos-consulta` | Prohibido |
| Dejar calendario 260 en esta vista | Prohibido (HIS no lo tiene) |
| Habilitar equipo | Prohibido D-TUR-17 |
| Implementar Excel POI en este slug | Prohibido — hijo |
| Cerrar G6 visual PDF con filas en este slug | Prohibido — hijo `turnos-agenda-consultas-pdf` |
| Re-portar `ConsultaAgenda.rptdesign` / embeber BIRT en Api | Prohibido — sidecar |
| Pedir smoke sin north HIS (111px, mismas filas) | Prohibido |
| `authPrimary` extra | Prohibido |
