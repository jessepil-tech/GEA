---
title: Pagaré — auditoría campo a campo centro de atención
status: deferred
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-auditoria-centro
---

# Auditoría `AUD_CENTRO_ATENCION` — `maestros-m1a-auditoria-centro`

Padre: [`maestros-m1a-centro/`](../maestros-m1a-centro/)  
Estado: **diferido** · 2026-09-18  
Canon: [`regla-paridad-acceso-auditoria.md`](../../../canon/regla-paridad-acceso-auditoria.md)

El dump tiene `AUD_CENTRO_ATENCION` / package `TBL_AUD_*`. M1a escribe
`ts.centro_atencion` con sello `fecha_last_update` / `actualizado_por` (último
cambio, no historial campo a campo). El destino **no** replica el historial.

**Riesgo:** baja o edición sin rastro viejo/nuevo en `AUD_*`.  
**Gatillo:** corte transversal de `TBL_AUD_*` o primer CU que exija historial de centro.
