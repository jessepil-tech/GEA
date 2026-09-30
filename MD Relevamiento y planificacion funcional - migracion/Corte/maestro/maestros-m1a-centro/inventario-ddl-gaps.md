---
title: Inventario DDL gaps — M1a centro de atención
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1a-centro.ddl
---

# Inventario DDL gaps — M1a

Tabla `ts.centro_atencion`: **en el dump** (2279; inventario 2026-08-20). Flyway **n/a**.

Padres GET: `ts.grp_centro_atencion` · `ts.provincia` · `ts.localidad` — también dump. Sin `CREATE`.

`AUD_CENTRO_ATENCION` existe; este corte **no** porta `TBL_AUD_*` → `diferido(auditoria)`.

NOT NULL dump (`pg_ts_columns`): `id_centro_ate` · `id_grp_centro_ate` · `centro_virtual`. El `(*)` del xhtml (nombre, calle, nro, provincia, localidad) **no** es NOT NULL en PG: la API/UI lo exige igual (paridad anunciador).

Gap vs Oracle de objeto faltante: **ninguno** para este CU. Si al implementar falta una columna en la PG **viva**, registrar en `pendientes-solo-oracle.md` y recién ahí un `Vnn` delta — no un seed con `ALTER`.
