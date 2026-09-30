---
title: Inventario geometría — T6.4 suspender grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.geometria
---

# Inventario geometría — Suspender / quitar suspensión

Fuente: `suspenderGrillaTurnos.xhtml` (north `size="150"`) · `quitarCancelacionGrillaTurnos.xhtml`. Familia T4 (`generacionGrillaTurnos` / `eliminarGrillaTurnos`).

| Pieza | HIS | Este corte |
|-------|-----|------------|
| Template | `contractDefault` north+center+south | igual familia T4 Web |
| Radio Opción | fila 200px / 40% | igual T4 |
| Labels | wrap | igual |
| Combos | `InputWid100` | igual |
| Fechas / horas | calendar + timePicker | igual T4 eliminar |
| Consultar | HIS: inline 150px (días en suspender/eliminar; fila motivo en quitar) | **D-TUR-71:** fila propia centrada 150px + lupa; igual Agenda/Consulta. Mismo nivel: eliminar · consulta agendas · quitar |
| Tabla candidatos | selección + cols turno | igual espíritu eliminar |
| Popup motivo | `p:dialog` width **400** height **60** (cuerpo; titlebar aparte) `closable="false"` `draggable="false"` `modal` `resizable="false"` header `#{msg.seleccione_motivo_liberacion}` | igual: panel 400px, closeOnBackdrop=false, sin X |
| Popup motivo fila | td label 100px + `InputWid100` combo | misma fila |
| Popup botones | Aceptar/Cancelar width 100px, panelGrid centrado | igual |
| North suspender | `size="150"` | compacto T4 |
| North quitar | `size="200"` + checkbox Obligatorio misma fila que radio | no apilar |
| Tabla | check 30 · fecha 52 · hora_desde 52 · hora_hasta 52 · duracion 52 · estado · paciente · centro · servicio · profesional_equipo · (quitar: motivo). **sortBy** en cada col de dato. Footer leyenda sobreturno/cancelado/reemplazado/inhibido. | mismas cols + anchos; **sort cliente** (helper T5 `turnos-agenda-sort`); leyenda |
| Equipo | tercera opción radio | **visible disabled** |

**No** tratar disposición como polish. Gate UI **antes** de template Angular.
