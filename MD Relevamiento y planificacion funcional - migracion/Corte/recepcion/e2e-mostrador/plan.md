---
title: Plan — E2E mostrador
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.e2e-mostrador
---

# Plan — E2E mostrador

No hay C0–C5 de UI nueva. Cortes de **ejecución del viaje**:

| # | Qué | Stop |
|---|-----|------|
| F0 | Contratar fixture: ids de paciente/centro/servicio/`LIBRE`/anunciador TV en `hospital_api` | si falta padre y nadie lo crea → `diferido(fixture)` de esa pata |
| F1 | Spec Playwright `Hospital-Web/e2e/e2e-mostrador.spec.ts` (sin mocks de agenda) | no mezclar con `turnos-agenda.spec.ts` |
| F2 | Recorrer: T5 otorgar → AGI → Cola B → POST Llamar → TV | evidencia `id` vigente (`turno` 5, cola B 5009, llamado 5). Filas 4/5008 se conservan |
| F3 | Ledger + Viaje Playwright: circuito `e2e-migrado` | mocks ≠ G6 |
| F4 | Medir 401 y NFR (p95 escritura + dos actores / `id_turno`) | 401 hecho 2026-09-17. NFR 2026-09-17: p95 Llamar **6,0 ms** (n=20 **200**, presupuesto ≤ 1,5 s); dos actores `id_turno=7` 200 vs 400 / 200 vs 404. Volumen fixture → `diferido(perf-volumen)` |

Ticket del tramo G1-D: solo si F0 tiene el PDF cobrado; si no, queda
`diferido` al corte de ticket AGI (no bloquea F1–F2).
