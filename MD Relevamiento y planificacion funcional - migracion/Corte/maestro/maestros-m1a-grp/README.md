---
title: SDD — M1a ABM grupo centro de atención
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp
---

# SDD — M1a grp (`maestros-m1a-grp`)

`phase_id:` **`sdd.hospital.maestros-m1a-grp`**  
Padre: [`maestros-m1a-centro/`](../maestros-m1a-centro/) · capa 3 [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/)

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Clarify · universo |
| [plan.md](plan.md) | Orden |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | dump `grp_centro_atencion` |
| [verify-report.md](verify-report.md) | **PASS / gate-done** 2026-09-18 · id=99002 · id=99003 · Playwright diferido |

**No es:** ABM `centro_atencion` (padre gate-done), west logo/sector/caja, auditoría `AUD_CENTRO_ATENCION`, M2 provincia.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7–8** · **gate-done** 2026-09-18 |
| Reservas | BODY n/a · Flyway n/a · writer `grp_centro_atencion` cobrado |
| Universo firmado | inventarios G0 de este slug |
| Fixture | resuelto · `id=99002` y `id=99003` vigentes dump |
| Evidencia | 9 verificado · 0 no verificado · 1 no ejecutado (Playwright) |
| Diferidos abiertos | Playwright `diferido(fixture)` · NFR `diferido(perf-volumen)` · `diferido(multi-instalacion)` |
| Próximo paso | Hijos west/auditoría siguen diferidos por gatillo |

Instalación: **TS** genérica · `diferido(multi-instalacion)`.

Jobs: `--jobs grp_centro` sin coincidencias → **N/A**.

Auditoría: **N/A** — Legacy-DB sin `TBL_AUD_GRP*CENTRO*`. Sello `fecha_last_update` / `actualizado_por` = último cambio.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Maestros | este workspace |
| BODY / package | **n/a** — JDBC/`NextIdService`; no BODY PERSONAS | libre |
| Rango Flyway | **n/a** | n/a |
| Tablas `ts` que escribe | `grp_centro_atencion` | writer de este corte |
| Rama | al implementar | — |

No escribe `centro_atencion`. Combo GET de M1a (`ListGrpCentroAtencionQuery`) sigue leyendo el dump.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.grp_centro_atencion` | **sí** (ABM 10217) | CU dump | `id=99002` POST · `id=99003` HAB |
| `ts.sec_id_tabla` (`GRP_CENTRO_ATENCION`) | no (contador) | seed `db/dev-seed/ts_maestros_m1a_grp.sql` | aplicado dump 2026-09-18 |
| Filas seed M1a `id=99001` | **no** (no son evidencia) | `ts_maestros_m1a_centro_padres.sql` | ya en dump |
