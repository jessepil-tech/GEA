---
title: Pagaré — auditoría campo a campo servicio / servicio_centro
status: deferred
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1b-auditoria-servicio
---

# Auditoría `AUD_SERVICIO_CENTRO` — `maestros-m1b-auditoria-servicio`

Padre: [`maestros-m1b-servicio/`](../maestros-m1b-servicio/)  
Estado: **diferido** · 2026-09-18  
Canon: [`regla-paridad-acceso-auditoria.md`](../../../canon/regla-paridad-acceso-auditoria.md)

Legacy escribe historial en `AUD_SERVICIO_CENTRO` (`TBL_AUD_SERVICIO_CTRO`). M1b
sella `fecha_last_update` / `actualizado_por` en `ts.servicio` y
`ts.servicio_centro` (último cambio, no campo a campo).

**Riesgo:** alta/edición/baja del par sin rastro viejo/nuevo en `AUD_*`.  
**Gatillo:** corte transversal `TBL_AUD_*` o CU que exija historial del vínculo.
