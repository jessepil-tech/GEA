---
title: Inventario interacción — turnos múltiples
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples.interaccion
---

# Inventario interacción

Fuente: `asignacionTurnos.xhtml` L69 · `turnosMultiples.xhtml` · `BBAgenda`.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Turnero Turnos Múltiples | click ítem | `idPMI=TurnosMultiples` + faces | mismo accordion; **no** tile |
| Agregar | botón 100px | fila en `listTurnosFiltroMultiple`; HIS limpia prestación/servicio/profesional; **no** consulta | `turnos-multiples-agregar`. North T5.1 compartido. **Ops 2026-09-14:** no limpiar north tras Agregar (2× CONS sin re-buscar). HIS L2628 sí limpia; desviación documentada. Consultar es el botón de la vista. |
| Trash carrito | icon trash | HIS: quita de `listTurnosFiltroMultiple`; `update` tabla carrito + `tablaTurnos` (no reconsulta días) | Si quedan filtros + convenio → reconsulta días del mes y grilla del día. Si el carrito queda vacío → **solo limpia** calendario + slots (sin POST). |
| Consultar | | pinta días disponibles (no escribe) | POST días |
| Click día calendario | `dateSelect` | llena `listTurnos` | POST grilla |
| Limpiar datos | | HIS redirect reset; **Web:** vacía carrito + slots + calendario (mismo que trash último; sin POST). Ops 2026-09-14. | |
| ⇄ cambiar horario | | popup LIBRES | **done** [`turnos-agenda-multiples-cambiar/`](../turnos-agenda-multiples-cambiar/) |
| Asignar Turno | south | si >1 centro → popup; si no reserva lote → infoTurno | |
| Popup centro Aceptar | | filtra slots del centro + reserva | |
| Popup centro Cancelar | | vacía tabla slots; no reserva | |
| infoTurno Asignar | | otorga N T5 | reusa dialog T5.3 |
| infoTurno Cancelar | | libera N T5 | |
