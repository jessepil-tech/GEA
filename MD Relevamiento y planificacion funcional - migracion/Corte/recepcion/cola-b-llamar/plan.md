---
title: Plan — Llamar fila Cola B
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cola-b-llamar
---

# Plan — Llamar Cola B

No hay C0–C5 de módulo nuevo. Cortes de **este** CU:

| # | Qué | Stop |
|---|-----|------|
| **C0** | Firma producto Clarify #4 = **B** (solo API) | **firmado** 2026-09-16 |
| **C1** | Extender `AnunciadorWritePort` + JDBC: path serv_amb + `ATENCION_MEDICA`; Cola A intacta; GM `f_set_paciente_anun_cola` | IT + `llamado` id=5 + GM PASS |
| **C2** | `POST` Llamar por `idColaEsperaServAmb` (command propio, no reusar body Cola A) → resolve anunciadores → WS | IT 200/401/404 |
| **C3** | UI según C0: **N/A** (API) | sin botón espera-amb |
| **C4** | Enganche `e2e-mostrador` live (turno 5 / cola B 5009 → TV) + ledger | ids + query; no fingir Cola A 4001 |

Ticket G1-D / reportes de `colaEspera`: **fuera**.

## Enfoque

| Capa | Decisión |
|------|----------|
| Persistencia | Reusar adapter; no hardcodear `CONSULTA` en este path |
| Resolución TV | `anunciador_ambiente_amb` (paridad `p_insert`); seed demo solo si el ambiente está vinculado |
| API | Command nuevo (`LlamarColaEsperaServAmb`) |
| UI | C0=B → N/A; **no** menú M5 |
| Ciclo TV | Sin cambios |

## Dependencias

- [`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) gate-done
- M4 lista Cola B gate-done
- Fixture `e2e-mostrador` (filas 4/5008 y 5/5009) — no borrar
- Hermano [`cu-llamar-atencion-medica/`](../cu-llamar-atencion-medica/): UI clínica E3 **después** de C1–C2

## Diferidos

| Capacidad | Destino |
|-----------|---------|
| Menú M5 Cola B | corte M5 (aún sin slug) |
| Cola servicio / en atención / cabecera | `cu-llamar-atencion-medica` |
| Jobs purga / Llamador | `relevamiento-procesos-programados` |
| Gate centro/box | `paridad-recepcion-gate` |
