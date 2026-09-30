---
title: Tasks — T5.1 Turnos agenda ficha paciente
version: 0.3.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-ficha-paciente
---

# Tasks — T5.1 · Camino 1

1. [x] **TSK-ops-g0-1** Abrir SDD + links T5/relevamiento — **2026-09-07**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** Camino 1 en [spec.md](spec.md) — **2026-09-07**
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md) **2026-09-07**
4. [x] **TSK-ops-g0-4** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md) **2026-09-07**
5. [x] **TSK-ops-g0-5** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md) **2026-09-07**
6. [x] **TSK-app-g1-1** Inventario gaps DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md) **2026-09-07**
7. [x] **TSK-app-g1-2** Flyway V48 seed ficha demo — **N/A** (V31/V47 alcanzan) **2026-09-07**
8. [x] **TSK-app-g2-1** Port lectura `TurnosAgendaFichaPort` + JDBC otros centros (subset) — **2026-09-07**
9. [x] **TSK-app-g2-2** CQRS + API GET pacientes/convenios/ficha/otros-centros + IT — **2026-09-07** (`TurnosAgendaFichaResourceIT`)
10. [x] **TSK-web-g3-0** Gate UI geometría DoD (north buscadores + west) — **2026-09-08**
11. [x] **TSK-web-g4-1** Dialogs buscador paciente/convenio — **2026-09-08**
12. [x] **TSK-web-g4-2** West: Desde/Hasta + leyenda cal + ficha + otros centros — **2026-09-08**
13. [x] **TSK-web-g4-3** Cablear north; quitar ids demo hardcode — **2026-09-08**
14. [x] **TSK-web-e2e** Ampliar `turnos-agenda.spec.ts` — **2026-09-08**
15. [x] **TSK-ops-g6-1** Smoke stack real — **2026-09-08** (ops)
16. [x] **TSK-ops-g6-2** Verify PASS + gobierno — **2026-09-08**

## Gate UI (G3) — xhtml

Ver [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md). Iteración 2026-09-08: time picker T4, colores HIS, grilla fija, buscadores sin cierre por `select` nativo.

## Viaje Playwright

| Viaje | Decisión |
|-------|----------|
| Buscar paciente + ficha west | e2e-migrado |
| Regresión T5 otorgar/liberar | e2e-migrado |
