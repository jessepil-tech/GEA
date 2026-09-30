---
title: SDD — Anunciador WebSocket
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.anunciador-ws
---

# Anunciador WebSocket

`phase_id:` **`sdd.hospital.anunciador-ws`** — active (abierto 2026-08-18)

Paridad de push para llamados (sustituye Socket.IO del Node).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Contrato evento / auth |
| [plan.md](plan.md) | Piezas |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger; gate **no cobrado** |

Relacionado: paridad recepción **M2 gate-done** — llamar en cola dispara el push WS (el writer no es este corte).

Inventario Node ↔ Api (qué cubre este WS y qué queda fuera):  
[`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/).

**Deuda:** ~~tras insert, las transiciones de estado (`S→N` / delete) deben volver a
empujar `nuevos-llamados`~~ — **hecho 2026-08-26** (consume-on-read + quitar publican peek).
Ver [`ciclo-vida-llamado-anunciador/`](../ciclo-vida-llamado-anunciador/).

## Estado del corte

**active — gate no cerrable.** Spec/plan/tasks en repo; verify abierto el 2026-09-15
para cumplir la compuerta estructural. Ledger: 0 `verificado`. No marcar PASS.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Satélite AGI + Anunciador | este workspace |
| BODY / package | n/a — canal WS Quarkus; no se porta package Oracle | libre |
| Rango Flyway | **ninguno**: no abre Vnn | n/a |
| Tablas `ts` que escribe | ninguna (empuja lista ya persistida por el writer de llamados) | lector/push |
| Rama | `dev/dev` (Api + Web) | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.anunciador` demo `1001` | no | seed / IT | padre del handshake WS |
| Fila de llamado para el push | no | CU llamar / IT | lo crea recepción / ciclo-vida |
