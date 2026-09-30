---
title: Inventario geometría — T6.5 reemplazo profesional
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.geometria
---

# Inventario geometría — Reemplazo profesional

Fuente: `reemplazoPersonalGrillaTurnos.xhtml`. Familia T4 (`generacionGrillaTurnos` / `eliminarGrillaTurnos` / T6.4).

| Pieza | HIS | Este corte |
|-------|-----|------------|
| Template | `contractDefault` north+center+south | igual familia T4 Web · **layout fill** |
| North | `pe:layoutPane` north (5 filas; sin `size` explícito) | compacto T4; no apilar |
| Profesional origen | label nowrap + input `InputWid100` width 40% + lupa `#{msg.buscar}` | misma fila · chrome **Agenda**: input + lupa + X pegados (sin botón texto «Buscar Profesional») |
| Centro / servicio | inputs **disabled** `InputWid100` | misma fila |
| Fechas / horas | calendar `dd/MM/yyy` + timePicker 50px · `mindate` = hoy | igual T4 eliminar · fecha **una línea** (T6.4) |
| Días + feriado | panelGrid columns 40 + spacers 10/40 | misma fila |
| Consultar | HIS: inline 150px con días | **D-TUR-71:** fila propia centrada 150px **después** de reemplazante+motivo (familia T4/T6.4: filtros → Consultar); no en medio del north |
| Reemplazante | input `Wid100` + lupa misma fila | sí · **fila propia**; mismo chrome Agenda (lupa + X) |
| Motivo | `selectOneMenu` `InputWid100` misma fila que reemplazante | **fila propia** (chrome T6.4 quitar motivo); copy HIS |
| Buscador dialog | 1200×550 `closable=false` `draggable=false` `modal` | reuso T2/T4 |
| Tabla | check 22 · fecha 52 · hora_desde 52 · hora_hasta 52 · duracion 52 · paciente · profesional · profesional_reemplazante · servicio. **sortBy** en cada col de dato. Fecha/hora nowrap `dd/MM/yy` / `HH:mm` | mismas cols + anchos; **sort cliente** (helper T5) |
| Footer leyenda | facet footer: reservado / sobreturno / reemplazado / suspendido / inhibido | **v1.19** `gt-his-consulta-leyenda` **fuera** del scroll; no `obsTableWrap` T4 |
| South | 3 botones 150px `MarAuto` (Reemplazar / Quitar / Volver) | igual; anclados, no viajan con filas |
| Equipo | no hay radio | N/A |

**No** tratar disposición como polish. Gate UI **antes** de template Angular.
