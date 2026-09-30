---
title: Tasks — T6.2 · cola Reasignación de Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 (Francisco «ok» 2026-09-16)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify
3. [x] **TSK-ops-g0-3** COUNT `ts.turno_a_reasignar` = **0** (2026-09-17 `grupogea-hospital_dev`). G6 espera T4 eliminar, **no seed**.
4. [x] **TSK-ops-g0-4** Gate UI: xhtml + inventarios **antes** de template/API
5. [x] **TSK-app-g2-1** GET lista cola JDBC (filtros HIS + call center)
6. [x] **TSK-app-g2-2** PATCH observaciones; DELETE cola al otorgar
7. [x] **TSK-app-g2-3** Tests handler (vacío, intervalo, obs)
8. [x] **TSK-web-g4-1** Accordion enabled; north/tabla/popup; south disabled + tooltip hijo
9. [x] **TSK-web-g4-2** Gear REASIGNAR TURNO → Agenda sin overlay; `idTurnoAReasignar` al otorgar
10. [x] **TSK-web-g5-1** e2e-migrado 4 viajes
11. [x] **TSK-ops-g6-1** Smoke Francisco 2026-09-17 «ok ahora pude probar el flujo» (T4 `302674` → Consultar → IrAGrilla → Otorgar; COUNT=0)
12. [x] **TSK-ops-g6-2** Verify PASS + gobierno (obs `302675`; acceso = padre T5; concurrencia PATCH)

## Gate UI

| Path | Rol | DoD |
|------|-----|-----|
| `turnosAReasignar.xhtml` | Hoja accordion | North 111px, tabla columnas HIS, south 150px, popup obs 700px |
| `asignacionTurnos.xhtml` L82–94 | Ítem accordion | Enable Reasignación; no tocar Avisos/Historial |

**Prohibido:** fork ruta; reabrir T6.1; portar TURNOS BODY; seed de cola; habilitar equipo; habilitar Imprimir/Excel; Historial; Avisos; T6 padre entero.
