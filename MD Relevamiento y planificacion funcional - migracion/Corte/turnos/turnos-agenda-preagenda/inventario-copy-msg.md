---
title: Inventario copy — T5.6 pre-agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-preagenda.copy
---

# Inventario copy — Pre-agenda (G0)

Fuente: `Resources.properties` + `MessageBundle`.  
Web: extender `turnos-agenda-labels.ts` (no inventar).

| msg.key / constante | Valor | En UI este corte |
|---------------------|-------|------------------|
| `pre_agenda_turnos` | Pre Agenda Turnos | ítem Turnero + header tabla |
| `paciente` | Paciente | north + col |
| `buscar` | Buscar | title lupa |
| `limpiar_datos_paciente` | (title closethick) | north |
| `servicio` | Servicio | north + col |
| `convenio` | Convenio | north (HIS **dos** veces: 2º = combo plan) + col |
| `plan` | Plan | col tabla (north usa key `convenio` en el 2º combo) |
| `fecha_desde` | Fecha Desde | north |
| `fecha_hasta` | Fecha Hasta | north |
| `consultar` | Consultar | botón |
| `fecha_prescripcion` | Fecha Prescripción | col 60px |
| `prestacion` | Prestación | col |
| `telefono` | Teléfono | col (`turno.telefonos`) |
| `acciones` | Acciones | col 60px |
| `ASIGNAR_TURNO` | ASIGNAR TURNO | menuitem overlay |
| `no_se_encontraron_registros` | (T5) | empty |
| `WRONG_INTERVAL_DATE_3` | La fecha hasta debe ser posterior a la fecha desde | toast |
| `reasignacion_turnos` | Reasignación Turnos | HIS `pageTitle` de esta hoja (bug copy); **no** usarlo en ítem Turnero |

STRING_TODOS_COMBO en servicio / convenio / plan.
