---
title: Tasks — Ciclo de vida llamado anunciador
version: 0.5.0
status: gate-done
owner: grupogea
last_updated: 2026-08-26
phase_id: sdd.hospital.ciclo-vida-llamado-anunciador
---

# Tasks — Ciclo de vida llamado

## C0 — Clarify

1. [x] **TSK-ops-c0-1** Rellenar tabla Clarify en [spec.md](spec.md) con evidencia (paths) — **2026-08-18**
2. [x] **TSK-ops-c0-2** Decidir: consumo-en-lectura `S→N` + ventana + DELETE negocio/re-call

## C1 — Api (núcleo TV)

3. [x] **TSK-app-c1-1** Puerto: update `llamar S→N`, delete re-call, listado filtrado por ventana — Flyway `V25`
4. [x] **TSK-app-c1-2** Consume-on-read en GET llamados (+ WS onOpen); peek tras insert
5. [x] **TSK-app-c1-3** Re-llamar mismo ticket: delete previo + insert `S`
6. [x] **TSK-app-c1-4** IT `llamados_consumeOnRead_segundaLecturaYaNoActivo`

## C2 — Disparadores

7. [x] **TSK-app-c2-1** Puerto `quitarPorPacienteServicio` + `quitarPorColaEsperaRecep` + IT — **2026-08-26**. Enganche anulación/atención = Fase 4.
8. [x] **TSK-app-c2-2** Scheduler safety `S→N` si `> 1h`
9. [x] **TSK-app-c2-3** Re-push WS tras consume-on-read `S→N` (RF-3) — **2026-08-26**

## C3 — Cierre

10. [x] **TSK-ops-c3-1** Verify-report actualizado (IT + criterio smoke) — **2026-08-26**
11. [x] **TSK-ops-c3-2a** Matriz Node + links B.1 / M2 (deuda ciclo cerrada) — **2026-08-26**
12. [x] **TSK-ops-c3-2b** Smoke display (API + UI WS) — **PASS 2026-08-26** (ver verify-report)
