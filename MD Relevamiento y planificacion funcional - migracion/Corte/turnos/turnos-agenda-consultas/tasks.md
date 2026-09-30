---
title: Tasks — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-consultas.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 — 2026-09-14 (opción 1 Imprimir)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-14
3. [x] **TSK-app-g2-1** Query consulta rango + IT (JDBC `ts.turno`) — 2026-09-14
4. [x] **TSK-app-g2-2** GET PDF `ReportsPort` `ConsultaAgenda` + IT stub (patrón T4) — 2026-09-14
5. [x] **TSK-web-g3-0** Gate UI north+tabla+south **antes** de template — geometría `consulta.xhtml` aplicada
6. [x] **TSK-web-g4-1** Turnero live + vista consulta + infoTurno + Imprimir
7. [x] **TSK-web-e2e** Viajes: abre vista; toast fechas; Consultar filas; Imprimir stub PDF
8. [x] **TSK-ops-g6-1** Smoke Francisco (UI + grilla; **no** PDF con valores) — 2026-09-15
9. [x] **TSK-ops-g6-2** Verify PASS + gobierno (PDF filas = hijo)

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `asignacionTurnos.xhtml` L79–81 | Turnero `consulta_agenda` | accordion 170; ítem live + highlight |
| `consulta.xhtml` L18–63 | Fila centro/servicio + profesional/equipo | label **111px**; `InputWid100`; profesional input+lupa; equipo disabled D-TUR-17 |
| L64–87 | Estado + convenio | convenio tipeable+lupa; estado Todos/Libre/Otorgado |
| L90–127 | Fechas + horas + check | fecha desde/hasta **misma celda** (HIS L90–107); horas+incluye sobreturnos **misma fila** (L109–125); time 65px |
| L129–132 | Consultar | icon search; no `authPrimary` extra |
| L142–207 | Tabla | scroll 100%; col info 24px; anchos HIS fecha 50 / horas 50 / doc 60 / paciente 120 … |
| L200–206 | Footer leyenda | 5 estilos T5; **fijo** bajo el scroll (HIS `facet footer`, no viaja con filas) |
| L210–222 | South Excel 120px + Imprimir 120px | Excel **visible disabled** + tooltip `diferido(turnos-agenda-consultas-export)`; Imprimir **live** (cable); **filas PDF** hijo [`turnos-agenda-consultas-pdf`](../turnos-agenda-consultas-pdf/) **gate-done** |

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Turnero → vista consulta | **e2e-migrado** |
| fechaDesde > fechaHasta → toast | **e2e-migrado** |
| Consultar → filas fixture | **e2e-migrado** |
| Imprimir → stub PDF | **e2e-migrado** (patrón T4). G6 visual filas = `turnos-agenda-consultas-pdf` |
| Legacy HIS | **no** |
