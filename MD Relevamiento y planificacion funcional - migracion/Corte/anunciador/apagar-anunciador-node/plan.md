---
title: Plan — Apagar ANUNCIADOR Node
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.apagar-anunciador-node
---

# Plan — Apagar Node

## Cortes

| Corte | Nombre | Entrega | Gate |
|-------|--------|---------|------|
| **N0** | Baseline + waivers | Clarify; W-NODE-1..3; smoke Node-down | **Done 2026-08-18** |
| **N1** | Ocupación (A3) | Flyway V21; `GET …/ocupacion-ambientes`; WS `ocupacion-ambientes-cambio` + poll; UI display compuesto | **Done 2026-08-18** |
| **N2** | Assets anunciador | Flyway V22; `GET …/logo` + `…/img-fondo` (data-URI); display Web + fallback | **Done 2026-08-18** |
| **N3** | Diccionario / TTS | Flyway V23; `GET …/diccionario`; flag `anunciadorConVoz`; SpeechSynthesis + lexema en Web | **Done 2026-08-18** |
| **N4** | Cutover config | Web sin `API_ANUNCIADOR`; Identity+Api only | **Done 2026-08-18** |
| **N5** | Decommission | Runbook + verify-report PASS (dev); `ANUNCIADOR/DEPRECATED.md` | **Done 2026-08-18 (dev)** |

## Dependencias

- `anunciador-ws` + display API Key: **prereq N0**.
- Datos ocupación: hoy Node lee Oracle/API legacy — N1 necesita puerto a PG
  (seed + adapter) o lectura acotada documentada.
- No depende de ux-shell-primefaces ni facade starter.

## Orden de trabajo sugerido

1. N0: smoke “Node off” solo caminos Angular ya migrados (documentar fallos).
2. N1 (A3) — mayor bloqueante funcional del Vue.
3. N2 mínimo (static) si no hay ABM.
4. N3 defer salvo TTS en sala.
5. N4–N5 corte.

## Riesgo

| Riesgo | Mitigación |
|--------|------------|
| Centros aún en Vue+Node | Cutover por centro; N5 no global hasta N0–N4 |
| Ocupación sin datos PG | Seed + contrato; sync Oracle→PG después |
| HOSPITAL_2 sigue llenando Oracle | Display nuevo no lee Node; dual-run temporal OK |
