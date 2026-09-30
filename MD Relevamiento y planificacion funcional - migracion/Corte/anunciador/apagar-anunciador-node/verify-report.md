---
title: Verify report — Apagar ANUNCIADOR Node
description: Envelope de calidad post N0–N5 (corte Node → Identity+Api+Web).
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.apagar-anunciador-node
---

# Verify report — `sdd.hospital.apagar-anunciador-node`

## metadata

| Campo | Valor |
|-------|--------|
| feature / slug | apagar-anunciador-node |
| date | 2026-08-18 |
| spec / plan / tasks | este directorio |
| analyzer | implementación N1–N3 + smoke N0/N4/N5 |

## verdict

**PASS** (dev local) — features de sala migradas; config Web sin Node; smoke Node-down OK; waivers W-NODE-1..3 firmados.

## Criterios de aceptación

| CA | Evidencia | Resultado |
|----|-----------|-----------|
| CA-1 Smoke display con Node down | `Hospital-Api/tools/smoke-node-down-anunciador.sh` → **PASS** (2026-08-18); listeners Node ausentes; Identity+Api 200; llamados/ocupación/logo/fondo/diccionario | **pass** |
| CA-2 Config Web sin URL Node | `Hospital-Web/public/appsettings.json` → solo Identity+Api+API Key; grep smoke N4 | **pass** |
| CA-3 N1–N3 | Flyway V21–V23; IT Anunciadores; SDD tasks checked | **pass** |
| CA-4 Runbook | [runbook-stop-node.md](runbook-stop-node.md) ejecutado en **dev** (Node no corría; smoke PASS) | **pass** (dev) |

## Waivers firmados (TSK-ops-3)

| Id | Qué | Decisión |
|----|-----|----------|
| W-NODE-1 | Reloj Socket `actualizar-hora` | **WAIVE** — reloj cliente |
| W-NODE-2 | Socket.IO byte-a-byte | **WAIVE** — WS Quarkus canónico |
| W-NODE-3 | Mirror 1:1 `LLAMADO_ANUNCIADOR` | **WAIVE** — `llamado_paciente` funcional |

No se WAIVEan ocupación, logos/fondo ni diccionario/TTS (Clarify cerrado).

## Checklist verify

| # | Criterio | Resultado |
|---|----------|-----------|
| V1 | Spec CA comprobables | pass |
| V2 | Plan N0–N5 coherente | pass |
| V3 | Tasks N1–N3 + N0/N4/N5 | pass |
| V4 | Smoke + IT | pass |
| V5 | Sin secretos nuevos | pass (demo-api-key ya conocido) |

## Warnings

| id | description |
|----|-------------|
| W1 | UAT/prod: sync BLOB logo/fondo y lexemas Oracle→PG pendientes |
| W2 | Campana = beep WebAudio (no `campana.mp3` legacy) |
| W3 | Código `ANUNCIADOR/` permanece en monorepo archivado; no borrar en este corte |
| W4 | N5 **global** solo cuando todos los centros del oleaje migraron (cutover por centro) |

## architecture_invariants

| invariant | status |
|-----------|--------|
| Display sin Node | ok |
| Auth Identity | ok |
| Realtime WS Quarkus | ok |

## Next

- Ops: inventario real de hosts UAT/prod ([inventario-despliegues-node.md](inventario-despliegues-node.md))
- Sync datos Oracle→PG para salas no-demo
- Archivar/retirar despliegues Vue+Node por centro
