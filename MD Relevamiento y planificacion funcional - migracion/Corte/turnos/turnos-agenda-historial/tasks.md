---
title: Tasks — T6.3 · Historial Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 D-TUR-70 (Francisco «dale arranquemos» 2026-09-18)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify
3. [x] **TSK-ops-g0-3** COUNT `ts.hist_turno` dump piloto: **17** total · **4** hoy (2026-09-18). G6 ve registros, **no seed**.
4. [x] **TSK-ops-g0-4** Gate UI: xhtml + inventarios **antes** de template/API
5. [x] **TSK-app-g2-1** GET lista hist JDBC (filtros HIS; **sin** call center en WHERE)
6. [x] **TSK-app-g2-2** GET info hist (ficha + prep/req/docs + `GET /pacientes/{id}/turnos`)
7. [x] **TSK-app-g2-3** Tests handler (vacío, hora invertida, estilo/label)
8. [x] **TSK-web-g4-1** Accordion enabled; north/tabla/leyenda; Excel disabled + tooltip hijo
9. [x] **TSK-web-g4-2** Info 1200×512 ficha/prep/req/docs/próximos
10. [x] **TSK-web-g5-1** e2e-migrado 5 viajes (info ahora con ficha/prep/req/docs/próximos)
11. [x] **TSK-ops-g6-1** Smoke Francisco lista: «ya veo registros en historial de turnos» 2026-09-18.
12. [x] **TSK-ops-g6-2** Verify PASS · info G6 «si se ve ok» · NFR «la 1 y la 2 bien» · acceso = padre T5

## Gate UI

| Path | Rol | DoD |
|------|-----|-----|
| `historialTurno.xhtml` | Hoja accordion | North 95/70, tabla cols HIS, popup 1200×512, Excel 150px disabled |
| `asignacionTurnos.xhtml` L92–94 | Ítem accordion | Enable Historial; no tocar Avisos |

**Prohibido:** fork ruta; portar TURNOS BODY; seed de hist; habilitar equipo; habilitar Excel; filtrar call center en SELECT; toast fechas `_3`; T6 padre entero; HOS-APP.
