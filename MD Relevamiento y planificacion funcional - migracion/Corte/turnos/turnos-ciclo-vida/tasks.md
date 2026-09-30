---
title: Tasks — T6 · migrar turno vencido
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-ciclo-vida.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 D-TUR-75 (Francisco 2026-09-21)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify (2026-09-21)
3. [x] **TSK-ops-g0-3** COUNT `ts.turno` con `fecha_hora_tur_ini < trunc(hoy)` = **0** (dump 2026-09-21); cola 6 h = 0
4. [x] **TSK-app-g2-1** Port JDBC archivo + copia mensajes + unlinks + DELETE (`FOR UPDATE`)
5. [x] **TSK-app-g2-2** Purga cola recepción/triage 6 h
6. [x] **TSK-app-g2-3** POST G6 + `@Scheduled` + flag dedicado
7. [x] **TSK-app-g2-4** Golden (2 tests) + dos actores POST 0 vs 1 + rollback FK 500 sin fila huérfana
8. [x] **TSK-web-g4-1** N/A (sin hoja)
9. [x] **TSK-web-g5-1** Playwright **N/A**
10. [x] **TSK-ops-g6-1** Smoke Francisco «ok» 2026-09-21 (`id=19999001` → `turno_vencido`; hoy `17255155` intacto)
11. [x] **TSK-ops-g6-2** Verify PASS + `verificar-sdd.sh turnos-ciclo-vida`

**Prohibido:** clonar WAR SCHEDULER; seed de `turno` / `turno_vencido`; portar `genera_mails` en este slug; `alter system disconnect`; reescribir expire 1 h; acoplar a `DataCleanupJob`; T6.1–T6.5.
