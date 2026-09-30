---
title: SDD — Anunciador WebSocket
description: Push nuevos-llamados para display sala (paridad Socket.IO).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.anunciador-ws
---

# Spec — Anunciador WebSocket

## Problema

El display legacy usa Socket.IO (`nuevos-llamados`). El piloto Quarkus quedó en
HTTP poll 5 s. El front Angular (starter) necesita push para paridad de sala
(campana / highlight inmediato).

## Resultado

1. `GET` REST se mantiene (fuente de verdad + fallback).
2. `WS /ws/anunciadores/{id}?apiKey=` empuja lista completa al conectar y tras cada insert.
3. Evento JSON: `{ "event": "nuevos-llamados", "payload": { "anunciadorId", "items": LlamadoPaciente[] } }`.
4. Flag: `features.enable-realtime` (`true` en `%dev` y `%test`).

## No objetivos

- Clonar protocolo Socket.IO / Node paths
- Redis backplane multi-réplica (UAT single-node OK)
- TTS en el Api
