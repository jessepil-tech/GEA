---
title: Inventario geometría — T6.2 cola reasignar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.geometria
---

# Inventario geometría — Cola Reasignación de Turnos

Fuente: `turnosAReasignar.xhtml`. Chrome padre = `asignacionTurnos.xhtml` (accordion 170px ya T5.2).

| Pieza | HIS | Este corte |
|-------|-----|------------|
| Labels north | `width: 111px` | igual |
| Combos | `InputWid100` | igual |
| Fecha/hora celdas | `width: 150px` | igual |
| Consultar / Limpiar | icon-only inline (search / closethick) | **D-TUR-66:** fila propia centrada, botones 150px con copy (como Agenda) |
| Col acciones | first, `width="60"` + gear 100% (recorta obs) | **D-TUR-67:** última col; 4.75rem; gear + obs visibles |
| Menuitem gear | `width: 147px` | igual |
| Col fecha | `64` · center | igual |
| Col hora | `52` · center | igual |
| Col duración | `52` · right | igual |
| Resto cols | sort + nowrap overflow | igual |
| South leyenda | 4 textos | igual |
| South Imprimir/Excel | `width:150px` (no 120) | **disabled** 150px |
| Popup obs | width **700**, `closable=false`, textarea 15 rows | igual |

**No** tratar disposición como polish. Equipo: misma celda; control disabled.
