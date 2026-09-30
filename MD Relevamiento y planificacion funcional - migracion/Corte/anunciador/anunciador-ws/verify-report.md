---
title: Verify — Anunciador WebSocket
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.anunciador-ws
---

# Verify — `anunciador-ws`

**Gate: no cobrado.** Hay spec, plan, tasks marcadas y código en repo
(`AnunciadorWebSocketIT`). Este archivo cierra el hueco estructural (faltaba
verify). **No** se re-ejecutó la IT ni el display en esta tanda: silencio no es PASS.

Canon: [`regla-evidencia-ejecutable.md`](../../../canon/regla-evidencia-ejecutable.md).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Push `nuevos-llamados` al conectar | en repo; no cobrado | `AnunciadorWebSocketIT.connect_receivesSnapshotAndPush` |
| Push tras persistir un llamado | en repo; no cobrado | publish en el writer de llamados · mismo IT |
| REST GET como fuente de verdad / fallback | en repo; no cobrado | display Angular poll 30 s |
| Protocolo Socket.IO Node | **WAIVE** | W-NODE-2 en [`apagar-anunciador-node/`](../apagar-anunciador-node/) |
| Redis backplane multi-réplica | N/A este corte | spec § No objetivos |
| TTS | N/A este corte | spec § No objetivos |
| Ciclo `S→N` / consume-on-read | N/A este corte | [`ciclo-vida-llamado-anunciador/`](../ciclo-vida-llamado-anunciador/) |

Ninguna capacidad está cobrada: el código no se acredita sin artefacto de esta tanda.

## Paridad UI (xhtml)

N/A. Este corte es el canal WS. No hay xhtml ni chrome nuevo; el display Angular
es consumidor. Geometría, copy, validaciones e interacción de sala viven en
[`piloto-agi-anunciador/`](../piloto-agi-anunciador/) y
[`ciclo-vida-llamado-anunciador/`](../ciclo-vida-llamado-anunciador/).

## Viaje Playwright

| Viaje | Decisión |
|-------|----------|
| Handshake WS / display | `N/A` este corte — e2e de sala en ciclo-vida / piloto |

## Ledger de evidencia

Auditoría **documental** 2026-09-15: se abrió el verify porque el linter exige el
archivo si hay `spec.md`. No se corrió Maven ni el display.

| # | Afirmación | Clase | Artefacto registrado | Ref | Verdicto |
|---|-----------|-------|----------------------|-----|----------|
| 1 | Port WS no es no-op; snapshot + push | test | clase `AnunciadorWebSocketIT`; **sin** comando ni conteo de esta tanda | tasks TSK-app-2 | **no verificado** |
| 2 | Display Angular abre WS con fallback poll | e2e | tasks TSK-web-1 marcada; **sin** selector, status ni trace | 2026-08-18 | **no verificado** |
| 3 | Re-ejecutar IT en esta tanda | test | — | 2026-09-15 | **no ejecutado** (compuerta documental; sin pedido de re-corrida) |

### Lectura

El corte puede estar implementado. El gate no cierra: 0 filas `verificado`.
Para cobrar: correr `AnunciadorWebSocketIT`, anotar comando + PASS/FAIL, y un
viaje de display con evento observado (o dejar el e2e en el slug de ciclo-vida
y marcar este N/A con el mismo criterio).
