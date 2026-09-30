---
title: Tasks — Paridad recepción / cola
version: 0.4.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.paridad-recepcion-cola
---

# Tasks — Paridad recepción / cola

## Ops

1. [x] **TSK-ops-1** Spec + plan + mapa legacy (dos colas)
2. [x] **TSK-ops-2** Clarify: PG destino + seed M1; sync → UAT; tablas recepcion/puesto
3. [x] **TSK-ops-3** Plan reviewed → status active

## M1 — Read-model Cola A

4. [x] **TSK-app-m1-1** Flyway tablas `cola_espera_recep` (+ FKs mínimas)
5. [x] **TSK-app-m1-2** Port lectura + `GET` pendientes / conteo prefijo
6. [x] **TSK-app-m1-3** IT lista

## M2 — Llamar

7. [x] **TSK-app-m2-1** Port `f_llamar_paciente_recepcion` (siguiente + id) + throttle 5s
8. [x] **TSK-app-m2-2** Side-effect display (`ts.llamado_anunciador` + WS)
9. [x] **TSK-app-m2-3** IT + verify-report

## M3 — UX Cola A

10. [x] **TSK-app-m3-1** UI puesto `/recepcion/cola`
11. [x] **TSK-app-m3-2** `GET …/ultimos` + IT
12. [x] **TSK-ops-m3** verify-report M1–M3

## M4 — Cola B

13. [x] **TSK-app-m4-1** Flyway `cola_espera_serv_amb` + seed
14. [x] **TSK-app-m4-2** `GET /api/v1/recepcion/espera-amb` + filtros + IT
15. [x] **TSK-app-m4-3** UI `/recepcion/espera-amb` + SDD/verify

## Diferido (no bloquea M1–M4)

- Schema 1:1 `LLAMADO_ANUNCIADOR` — **superseded:** ya en `ts.llamado_anunciador` (V27);
  display hoy = `ts.llamado_anunciador`. Avance 2026-08-25 (cutover ts); `llamado_paciente` retirado en V33
- Sync extract Oracle → PG (UAT/pre-corte)
- Acciones menú Cola B (anular, CI, print…) → M5 futuro
- Catálogos desplegables servicio/profesional desde maestro (hoy filtros por id + seed labels)
