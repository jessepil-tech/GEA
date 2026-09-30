---
title: Interacción — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-hab-equipo.interaccion
---

# Interacción

| Disparador | Qué hace | Nota |
|------------|----------|------|
| Buscar (north) o Enter en Equipo | `buscarEquipoServCentro` | manda solo el texto. LIKE en `item_equipo.equipo`, centro o servicio. No reenvía el centro ni el servicio ya elegidos. Siempre abre el dialog (decisión de producto 2026-09-23: no entra directo con un hit). Texto vacío sin aviso. Cero: aviso y dialog. El dialog queda en Todos |
| Dialog buscar | `visible` atado a `displayBuscadorEquipoServCentro` | `closable="false"` → no cierra por backdrop. No abrir al render de la página |
| Filtros del buscador | centro, servicio, texto equipo + lupa | centro recarga servicios |
| Fila del buscador | `rowSelect` → `setEquipoServCentroBuscado` | cierra y lista vigencias |
| Volver | `setEquipoServCentroBuscado(null)` | **vacía** el north y la lista |
| Aceptar del buscador | solo si `multiple` | esta hoja no es múltiple: el botón no se pinta |
| Agregar | abre popup en alta | disabled sin equipo |
| Lápiz | abre popup en edición | fecha vigencia disabled |
| Tacho | confirm y baja | modal DS, no `window.confirm` |
| Aceptar popup | persiste y refresca la grilla | toast |
| Cerrar popup | `immediate` | no persiste |
| Check de tope diario | ajax habilita los dos máximos | misma fila |
| Checks de la grilla | disabled | solo lectura |

Paginado de la grilla: 12 filas.
