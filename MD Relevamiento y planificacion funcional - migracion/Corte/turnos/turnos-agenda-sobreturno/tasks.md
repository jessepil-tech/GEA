---
title: Tasks — T5.2 sobreturno agenda
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno
---

# Tasks — Sobreturno agenda

1. [x] **TSK-ops-g0-1** Abrir SDD + link T5 RF-7 / relevamiento / backlog — **2026-09-10**
2. [x] **TSK-ops-g0-2** Clarify **FIRME Camino 2** en [spec.md](spec.md) — **2026-09-10** (ok firme)
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md)
4. [x] **TSK-ops-g0-4** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md) (accordion 170)
5. [x] **TSK-ops-g0-5** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md)
6. [x] **TSK-app-g1-0** Inventario DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md)
7. [x] **TSK-app-g2-1** API huecos: **N/A** — `GET …/grilla` T5 cubre grilla del día + turnos hoy; sin Flyway/endpoint nuevo
8. [x] **TSK-web-g3-0** Gate UI: accordion L20–108 + popup L316 **antes** template — notas en esta hoja
9. [x] **TSK-web-g4-1** Web: accordion Camino 2 + disparador Sobreturno + popup HIS
10. [x] **TSK-web-g4-2** Aceptar → POST sobreturno → infoTurno otorga T5; Volver sin INSERT; turnos de hoy
11. [x] **TSK-web-e2e** Ampliar `turnos-agenda.spec.ts` — 21 passed 2026-09-10
12. [x] **TSK-ops-g6-1** Smoke stack real (Francisco) — **PASS** 2026-09-10
13. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-10**

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `…/asignacionTurnos/asignacionTurnos.xhtml` L20–108 | Accordion west `size="170"` tabs Paciente+Turnero `activeIndex="0,1"`; south Acciones/Volver `width:100%` | Columna **170px** a la izquierda del calendario 260px; ambos tabs abiertos; ítems disabled visibles |
| mismo L72–75 | Menú Turnero `Sobreturno` (`disabled` si `idPMI ne 'Agenda'`) | No gear; click → popup o toast paciente |
| mismo L316–444 | `$popupSobreturno` | header `Sobreturno`, `closable=false`; label col **111px**; prestación input+lupa misma fila; horas misma fila; centro+servicio misma fila; profesional+equipo misma fila; Aceptar/Volver `MarAuto` 2 cols. **No** X. **No** `authPrimary`. Equipo disabled. |
| `…/asignacionTurnos/agenda.xhtml` L600–617 | `$popUpInfoTurnosDeHoy` (camino Aceptar) | header `Información`, Aceptar/Cerrar, `closable=false` |
| mismo L267 | Columna grilla `motivo_sobreturno` | ya T5 si DTO trae texto |

Inventarios copy/validaciones/interacción: **G0 hecho**.  
**Prohibido** pedir smoke sin paridad xhtml (salvo diferido listado: ABM Paciente, otras `.faces`, equipo usable, T7).

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Sin paciente → toast | e2e-migrado |
| Con paciente → abre popup | e2e-migrado |
| Aceptar → otorga sobreturno | e2e-migrado |
| Turnos de hoy | e2e-migrado |
| Accordion chrome (Agenda highlight, ítem disabled) | e2e-migrado |
| Legacy HIS | no |
