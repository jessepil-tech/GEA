# CU-B.1 — Llamar recepción (paridad legacy)

**Estado:** **gate-done** (2026-08-14) — acto de **publicar** llamado.  
**Legacy:** `btnLlamar` / `actLlamar` en recepción HOSPITAL_2.

- [spec](spec.md) · [plan](plan.md) · [tasks](tasks.md) · [verify](verify-report.md)

Endpoint: `POST /api/v1/agi/recepciones/{id}/llamar` → `ts.llamado_anunciador`.  
UI: `/agi/espera` → **Llamar**.

### Cuándo se publica en el anunciador

| Acto | ¿TV? |
|------|------|
| Confirmar recepción AGI (`/agi/recepcion`) | **No** |
| **Llamar** en Espera AGI o Cola recepción | **Sí** |

El tótem/recepción solo deja al paciente en espera; el anuncio es **manual** (paridad `btnLlamar`).

**Ciclo `LLAMAR` (deuda B.1):** **cerrada** en núcleo TV —  
[`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) **gate-done** (2026-08-26).  
Enganche de `quitar` a CUs de anulación/atención = Fase 4 (fuera de este acto).

Mapa UI: [`mapa-menu-hospital-web.md`](../../../relevamiento/mapa-menu-hospital-web.md).
