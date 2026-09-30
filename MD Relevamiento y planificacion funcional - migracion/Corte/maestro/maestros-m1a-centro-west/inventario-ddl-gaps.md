---
title: Inventario DDL — M1a logos
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro-west.ddl
---

# Inventario DDL — dump `ts.pack_logos` (2026-09-18)

Objeto en dump. **No Flyway.** COUNT pack = 0. `centro_atencion` 1001/1002 con `id_pack_logos` NULL. Sin `sec_id_tabla` PACK_LOGOS (seed contador). Sin `TBL_AUD_PACK*`.

| Columna | Tipo |
|---------|------|
| `id_pack_logos` | numeric PK |
| `logo_impresion_small` | bytea |
| `logo_app` | bytea |
| `logo_impresion` | bytea |
| `logo_comp_int` | bytea |
| `logo_term_auto_recep` | bytea |
| `fecha_last_update` | timestamp |
| `actualizado_por` | varchar |
| `logo_prescripciones` | bytea |
| `logo_hc` | bytea |

FK leída/escrita: `ts.centro_atencion.id_pack_logos`.
