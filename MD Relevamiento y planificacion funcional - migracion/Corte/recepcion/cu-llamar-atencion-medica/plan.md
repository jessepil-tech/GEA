---
title: Plan — CU Llamar atención médica
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Plan — Llamar atención médica

## Cortes

| Corte | Qué | Exit |
|-------|-----|------|
| **E0** | Clarify firmado (spec) + links matriz/mapa | CA-1 |
| **E1** | Extender `AnunciadorWritePort` (+ adapter): path clínico cola serv amb | **Owner:** [`cola-b-llamar/`](../cola-b-llamar/) C1 |
| **E2** | `POST` recurso clínico (cola + ambiente) → resolve anunciadores → insert + WS | IT endpoint |
| **E3** | Gate UI cola personal + Llamar (`/ambulatoria/espera-atencion`) | Smoke UI **cobrado** 2026-09-16 |
| **E4** | verify-report + smoke C-style (llamar → GET1 S → GET2 N → quitar) | Gate PASS |

## Enfoque técnico

| Capa | Decisión |
|------|----------|
| Persistencia | Reusar adapter; **no** hardcode `tipo_atencion='CONSULTA'` en path clínico |
| Resolución TV | `anunciador_ambiente_amb` por ambiente del puesto (paridad `p_insert`) |
| API | Nuevo command (ej. `LlamarColaEsperaServAmb`) — no reusar body de Cola A |
| UI | Pantalla clínica dedicada; seed filas `cola_espera_serv_amb` (IT ya siembra 5001/5002) |
| Ciclo TV | Sin cambios — consume-on-read + WS ya gate-done |

## Dependencias

- [`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) gate-done
- Read-model Cola B / `cola_espera_serv_amb` (listado existente puede informar seed; **no** implica Llamar en `/recepcion/espera-amb`)
- Seed anunciador↔ambiente en DEV

## Diferidos (slugs)

| Capacidad | Slug tentativo |
|-----------|----------------|
| Llamar cola servicio / en atención / cabecera | `cu-llamar-atencion-medica-ampliar` (o tasks E+) |
| Quitar al cancelar atención | Fase 4 ciclo-vida / CU atención |
| Auto-atender al llamar | hijo UX |
| Clarify Llamar en Cola B recepción | `paridad-recepcion-cola` M5 / Clarify |
| GYE / consultorio | slugs P1 siguientes |
