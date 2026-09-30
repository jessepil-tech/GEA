---
title: Tasks — turnos múltiples agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** — 2026-09-11 (Francisco «ok firme»)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-11
3. [x] **TSK-app-g1-1** DTO filtros / outcome; sin Flyway tmp
4. [x] **TSK-app-g2-1** POST días + POST grilla (CQRS, JDBC `ts`)
5. [x] **TSK-app-g2-2** POST `reservar-multiples` un TX
6. [x] **TSK-web-g3-0** Gate UI vista + popup centro **antes** de template
7. [x] **TSK-web-g4-1** Turnero + carrito + consultar/calendario + Asignar + infoTurno tabla
8. [x] **TSK-web-e2e** Viajes 2 filtros + convenio faltante
9. [x] **TSK-ops-g6-1** Smoke Francisco — 2026-09-14 (visto bueno)
10. [x] **TSK-ops-g6-2** Verify PASS + gobierno — 2026-09-14

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `asignacionTurnos.xhtml` L69–71 | Turnero `turnos_multiples` | accordion 170; ítem live |
| `turnosMultiples.xhtml` L24–247 | North form + Agregar 100px | `InputWid100`; prestación (*) + cod 150px; equipo disabled |
| L249–270 | Tabla carrito | `scrollHeight="151"`; trash 24px |
| L271–278 | Consultar / Limpiar datos | MarAuto; spacer 20px |
| L279–305 | Tabla turnos | header `Turnos {fecha}`; hora 52; duración 20; ⇄ **done** hijo |
| L308–316 | South Asignar Turno | disabled si tabla vacía |
| L319–421 | West 260 calendario | header Días Disponibles; legend 6 estados; desde/hasta 45px |
| L423–473 | West ficha paciente | ya T5.1; ocultar otros centros en esta vista |
| L506–529 | `$popupSeleccionCentroAtencion` | width 450; `closable=false`; Aceptar/Cancelar |
| L540 | `$popupCambiarTurno` | **done** hijo [`turnos-agenda-multiples-cambiar/`](../turnos-agenda-multiples-cambiar/) gate-done 2026-09-14 |

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Turnero → 2 filtros → Consultar → día → Asignar → infoTurno | e2e-migrado |
| Consultar sin convenio → toast | e2e-migrado |
| Legacy HIS | no |
