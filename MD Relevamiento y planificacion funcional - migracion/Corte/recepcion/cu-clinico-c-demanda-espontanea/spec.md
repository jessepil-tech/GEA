---
title: SDD — CU clínico C · Demanda espontánea
description: Alta recepción sin turno previo (camino feliz).
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-17
phase_id: sdd.hospital.cu-clinico-c-demanda-espontanea
---

# Spec — CU-C Demanda espontánea

Padre: [`cu-clinico/`](../cu-clinico/).

## Problema

G1 asume turno seed. En legacy hay alto volumen de **demanda espontánea** (sin
turno): identificar → elegir servicio → cola recepción/atención.

## Decisión de modelo

**C1** — `turno_id` nullable en `recepcion_agi` + columna `servicio` denormalizada
(Flyway `V17`). No turno sintético ni tabla aparte.

## Resultado (entregado)

1. `GET /api/v1/agi/servicios` (catálogo seed `servicio_agi`).
2. `POST /api/v1/agi/recepciones/espontanea` (paciente + servicioId/servicio + terminal).
3. Ticket PDF (mismo camino G1-d).
4. UI wizard `/agi/demanda-espontanea`.
5. IT + smoke.

## Requisitos

| Id | Requisito | Estado |
|----|-----------|--------|
| RF-1 | Identificar paciente (reusa G1) | ✅ |
| RF-2 | Elegir servicio (catálogo seed) | ✅ |
| RF-3 | Confirmar espontánea → ticket | ✅ |
| RF-4 | NextId `COLA_ESPERA_RECEP` | ✅ |
| RF-5 | UI wizard | ✅ |
| NFR-1 | Sin ValidadorWS online v1 | ✅ |

## No objetivos

- Prestaciones / profesionales complejos
- Facturación
- Triage

## Criterios de aceptación

1. Paciente sin turnos puede generar ticket espontánea. ✅
2. PDF descargable. ✅ (mismo endpoint ticket)
3. Aparece en lista CU-B. ✅
4. Smoke PASS. ✅
