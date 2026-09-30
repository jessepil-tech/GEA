---
title: SDD — Piloto AGI G1-b elegibilidad
description: Gate ValidadorWS (puerto + seed) y destino de ticket; sin hardware ni WS real.
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-b
---

# Spec — Piloto AGI G1-b (elegibilidad)

`phase_id:` `sdd.hospital.piloto-agi-g1-b`  
Padre: [piloto-agi-g1](../piloto-agi-g1/) (**gate-done**).  
Orden: [backlog-orden-2026-08-14](../../../planificacion/backlog-orden-2026-08-14.md) §1.

## Problema

G1 midió el camino feliz sin el **gate administrativo** que el WAR aplica con
`ValidadorWS` (elegibilidad / flags de convenio → atención vs cola de recepción
humana). Sin ese puerto, el piloto no mide el acoplamiento real de recepción.

## Resultado deseado

1. Puerto de dominio `ValidadorElegibilidadPort` (no cablear `VALIDADORES.jar`).
2. Adapter **seed** con reglas explícitas (autorizado / rechazado por documento).
3. Al confirmar recepción: si el convenio exige elegibilidad online y no autoriza →
   ticket destino `ESPERA_RECEPCION` + mensaje; si autoriza o no exige → `ESPERA_ATENCION`.
4. Auth: JWT Identity **o** API Key (mismo mecanismo que anunciador) en endpoints G1.
5. UI: mostrar destino + mensaje en el ticket.
6. Smoke + IT cubren ambos caminos.

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Seed convenio + flags `req_validador_online` / `req_elegibilidad` ligados al paciente |
| RF-2 | `ValidadorElegibilidadPort.evaluar(paciente, convenio)` → autorizado + mensaje |
| RF-3 | Confirmar recepción ramifica destino ticket según RF-2 |
| RF-4 | Paciente seed autorizado (DNI 30111222) y rechazado (DNI 30999888) |
| RF-5 | Endpoints G1 aceptan Bearer **o** `X-API-Key` demo |
| RF-6 | Front muestra `destino` + `mensajeValidador` |
| NFR-1 | Sin clientes WS reales (Activia/ITC/…) en este slice |
| NFR-2 | Sin impresora / lectora física (WAIVE hardware) |
| NFR-3 | Sin golden master Oracle de elegibilidad (diferido; seed = oráculo del slice) |
| INV-1 | Identity sin datos clínicos de elegibilidad |
| INV-2 | No microservicio Core |

## Criterios de aceptación

1. DNI 30111222 + turno → 201 `destino=ESPERA_ATENCION`, `autorizado=true`.
2. DNI 30999888 + turno → 201 `destino=ESPERA_RECEPCION`, `autorizado=false`, mensaje no vacío.
3. Sin auth → 401; con `X-API-Key: demo-api-key` → flujo OK (al menos terminales o identificar).
4. UI ticket muestra destino (atención vs recepción humana).
5. verify-report PASS.

## No objetivos

- Implementar `WSValidadorClient` / proveedores online
- `convenioReqToken` / `convenioReqVersionCredencial` UI (G1-c)
- Impresión BIRT / cola Oracle
- Golden master VPN de elegibilidad

## Defaults clarify (aceptados 2026-08-14)

| Q | Decisión |
|---|----------|
| Q1 Adapter | Seed en Postgres, no WS real |
| Q2 Hardware | WAIVE |
| Q3 Auth terminal | API Key existente del starter |
