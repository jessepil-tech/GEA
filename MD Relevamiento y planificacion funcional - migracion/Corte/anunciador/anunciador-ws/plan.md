---
title: Plan — Anunciador WebSocket
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.anunciador-ws
---

# Plan

| Pieza | Detalle |
|-------|---------|
| Port | `AnunciadorRealtimePort.publishLlamados` |
| Endpoint | `/ws/anunciadores/{anunciadorId}?apiKey=` |
| Trigger | `JdbcAnunciadorWriteAdapter.insertLlamado` → publish lista |
| Web | Display: WS + poll fallback 30 s |
| Auth | Misma demo API Key que REST display |

## Orden

1. [x] Api WS + push + IT
2. [x] Hospital-Web display
3. [x] Docs SDD
