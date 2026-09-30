---
title: Inventario DDL gaps — T5.1 hijo info popups
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-info-popups.ddl
---

# Inventario DDL gaps — Info popups (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway Api? | ¿Bloquea v1? | Acción |
|--------|-----------------|--------------|--------|
| `ts.convenio.descripcion` | **Sí** V31 (API T5.1 ya lee) | No | Usar |
| `ts.plan_convenio.observaciones` | **Sí** V31 | No | Usar |
| `ts.doc_req_plan_conv` | **No** (Reports seed usa `public.*`) | No v1 | **diferido** elegibilidad |
| `ts.doc_req_prest_plan` | **No** | No v1 | **diferido** |
| `ts.doc_requerido` | **No** | No v1 | **diferido** |
| Asset `InfoBusquedaPac.png` | N/A (estático HIS) | No | `Hospital-Web/public/images/turnos/` |

G1: **N/A** — no Flyway en este corte.
