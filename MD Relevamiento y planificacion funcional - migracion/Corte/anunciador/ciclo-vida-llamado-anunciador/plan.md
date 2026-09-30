---
title: Plan — Ciclo de vida llamado anunciador
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.ciclo-vida-llamado-anunciador
---

# Plan — Ciclo de vida llamado

## Cortes

| Corte | Nombre | Entrega | Gate |
|-------|--------|---------|------|
| **C0** | Clarify + evidencia | Tabla firmada; decisión consumo-en-lectura | **CA-1 PASS** (2026-08-18) |
| **C1** | Puerto + consume-on-read | `S→N` al GET/WS snapshot; filtro ventana; re-call delete+insert | CA-2 parcial |
| **C2** | Disparadores | DELETE anulación/quitar; scheduler safety 1h | CA-2–3 |
| **C3** | Verify | IT + smoke + matriz + padres | CA-4 |

## Orden

1. ~~C0~~ **done**.
2. C1 (núcleo paridad TV) → C2 (negocio + safety).
3. C3 y marcar deuda B.1/M2 cerrada.

## Implicación C1 (desde Clarify)

El “desrojo” no va en el `POST …/llamar` del paciente siguiente: va en el
**camino de lectura** del display (como `f_get_paciente_anunciar`), más deletes
de re-call / anulación.

## Riesgo

| Riesgo | Mitigación |
|--------|------------|
| Consumo en GET cambia semántica REST | Documentar side-effect; IT + smoke; mismo contrato WS |
| Varios `S` acumulados | Consumir el más viejo por snapshot (paridad MIN fecha) |
| Borrar histórico útil | Preferir `S→N` + ventana; DELETE solo donde legacy borra |

> **Avance 2026-08-25 (cutover ts):** el display ya usa `ts.llamado_anunciador` (V27, ids
> NUMERIC string en JSON); `llamado_paciente` / `public.*` retirado en **V33**. Schema canónico
> según [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).
