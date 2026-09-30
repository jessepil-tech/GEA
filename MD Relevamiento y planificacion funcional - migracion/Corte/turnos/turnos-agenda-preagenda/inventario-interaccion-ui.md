---
title: Inventario interacción — T5.6 pre-agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-preagenda.interaccion
---

# Inventario interacción UI — Pre-agenda (G0)

Fuente: `asignacionTurnos.xhtml` L89–91 · `preAgendaTurnos.xhtml` · `BBPreAgendaTurnos`.

Layout HIS hoja: **[accordion 170] [north filtros | tabla fill]**. **Sin** calendario 260. **Sin** south.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Turnero Pre Agenda Turnos | `actionSelectMenu('PreAgendaTurnos', preAgendaTurnos.faces)` | cambia hoja | `vista=preagenda`; highlight; oculta calendario Agenda |
| Turnero Agenda | agenda.faces | vuelve | `vista=agenda` |
| Paciente input+lupa | change / click `buscarPaciente` | popup paciente | tipeable + buscador T5.1 |
| Limpiar paciente | `limpiarDatosPaciente` | vacía | botón closethick |
| Servicio combo | `actChangeConfiguracionTurno` | recarga combo | GET combos T5 |
| Convenio combo | `onChangeConvenio` | recarga planes | GET planes del convenio |
| Plan combo | — | filtro Consultar | select |
| Fecha desde/hasta | calendar **sin** mindate hoy | default −3m / hoy | `input type=date` |
| Consultar | `actBtnConsultar` | llena `listTurnos` | GET query; no escribe |
| Acciones → ASIGNAR TURNO | `actBtnIrAGrilla` | redirect Agenda + session | `vista=agenda` + prefijo north + consultar grilla + `idPreAgendaTurno` |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Fork `/turnos/pre-agenda` | Prohibido |
| Confundir con `consultaPreagenda.xhtml` | Prohibido (otra ruta menú) |
| Dejar calendario 260 en esta vista | Prohibido (HIS no lo tiene) |
| Insertar filas desde esta hoja | Prohibido (ATENCION) |
| Paginador | Prohibido (HIS scroll 100%) |
