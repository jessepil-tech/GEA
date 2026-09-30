---
title: Inventario geometría — T6.3 historial turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.geometria
---

# Inventario geometría — Historial Turnos

Fuente: `historialTurno.xhtml`. Chrome padre = `asignacionTurnos.xhtml` (accordion 170px ya T5.2).

| Pieza | HIS | Este corte |
|-------|-----|------------|
| Label centro | `td width: 95px` | **111px** — 95 recorta «Centro Atención» en text-sm (mismo ancho que Consulta/Cola) |
| Label servicio | `td width: 70px` | igual |
| Combos | `InputWid100` | igual |
| Profesional | `panelGrid` input+lupa | chrome campo v1.15 |
| Equipo | misma fila que profesional | visible **disabled** |
| Fechas | misma `td`: desde + spacer 10 + hasta; calendar `size=9` `dd/MM/yy` | igual |
| Horas | misma `td`: desde + spacer 10 + hasta; `pe:timePicker` spinner | igual |
| Consultar | última `td` fila 3, **value + icon search** | fila propia centrada 150px (D-TUR-71; igual Agenda/Consulta; HIS lo deja inline) |
| Col info | first, `width="24"` icon-only | igual |
| Col fecha / hora / duración | `50` center (duración right) | igual |
| Col estado | `66` | igual |
| Col paciente / centro / servicio / prof-eq / prest | `120` nowrap | igual |
| Col teléfono | `90` | igual |
| Col cod prest | `60` | igual |
| Col fecha mod | `130` center | igual |
| Col usuario mod | `120` | igual |
| Col tipo sol / canc | `80` | igual |
| Tabla | `scrollable` `scrollHeight=100%` `sortMode=single` | fill |
| Leyenda | inner south, 4 textos | igual |
| Excel | outer south `Wid150px` | **disabled** 150px |
| Popup info | width **1200**, height **512** (cuerpo), `closable=true`, `modal`, no drag | igual · panel `!max-w-[1200px]` (D-TUR-72: `sizeXl` 1024 recortaba) |
| Popup paneles | `datos_turno` + `preparacion_previa` (128px overflow) misma fila; `datos_paciente` + `proximos_turnos` rowspan; req 350px + docs 350px | igual topología; req/docs fila **~176–196px** (HIS `scrollHeight=80` no muestra header+fila) |
| Próximos | `scrollHeight=240`; fecha/hora 52 | fecha **4.75rem** / hora **3.75rem** (52px + padding 12 recortaba la celda); resto 1fr; wrap llena el rowspan |
| Req / docs | `scrollHeight=80` | wrap **sin** tope 80; la fila del grid es 176–196px para ver header + filas |
| Volver | `panelGrid MarAuto` | igual |

**No** tratar disposición como polish. Disabled = `#dadada`.
