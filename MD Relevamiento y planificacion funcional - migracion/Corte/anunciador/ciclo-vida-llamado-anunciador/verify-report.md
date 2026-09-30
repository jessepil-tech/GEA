---
title: Verify — Ciclo de vida llamado anunciador
version: 0.4.0
status: gate-done
owner: grupogea
last_updated: 2026-08-26
phase_id: sdd.hospital.ciclo-vida-llamado-anunciador
---

# Verify-report — Ciclo de vida llamado

## Resultado

**GATE-DONE** — 2026-08-26  

| Pieza | Estado |
|-------|--------|
| C0–C2 código + IT | **PASS** |
| C3.2a matriz + padres B.1/M2 | **PASS** |
| C3.2b smoke display | **PASS** (API + UI display WS) |

Schema: `ts.llamado_anunciador` (V27); piloto retirado **V33**.

## Smoke C3.2b — evidencia 2026-08-26

Stack: Identity `:8080` UP · Api `:8081` UP · Web `:4200` · PG `10.0.0.35:10520/grupogea-hospital_dev-test`.

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Display `/display/anunciadores/1001` | OK — header “Anunciador Demo”, “Tiempo real (WebSocket)” |
| 2 | `POST /api/v1/recepcion/colas/2001/llamar` cola `4003` puesto `3001` | OK — `paciente=B-201`, `llamar=true`, `anunciadorId=1001` |
| 3 | `GET …/anunciadores/1001/llamados` (1ª) | OK — `llamar=true` (rojo / activo) |
| 4 | `GET …/llamados` (2ª = consume) | OK — mismo id, `llamar=false` (S→N) |
| 5 | Display UI tras llamar | OK — banner **B-201 / BOX 1** + WS |
| 6 | Re-call mismo ticket `4003` | OK — `count(*)=1` en `ts.llamado_anunciador` para esa cola; nuevo id `9`, luego S→N |

Firma: agente Cursor (API + screenshot display) · Fecha: **2026-08-26**

## IT

```bash
cd Hospital-Api
mvn -pl presentation-api -am \
  -Dtest=AnunciadoresResourceIT,AnunciadorWebSocketIT \
  -Dsurefire.failIfNoSpecifiedTests=false test
```

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Consume `S→N` en lectura TV | **done** | C1 + smoke |
| Ventana minutos | **done** | C1 |
| Re-call delete+insert | **done** | C1 + smoke #6 |
| Safety `S→N` >1h | **done** | C2.2 |
| DELETE anulación (`f_quitar`) | **puerto done**; enganche CUs → **Fase 4** | C2.1 |
| Push WS tras transición | **done** | C2.3 + display “Tiempo real (WebSocket)” |
| Smoke display firmado | **done** | C3.2b |
| Links B.1 / M2 / matriz | **done** | C3.2a |

## Nota C2.1

Disparadores de anulación/atención aún no existen en Api. Puerto listo; sin UI inventada.
