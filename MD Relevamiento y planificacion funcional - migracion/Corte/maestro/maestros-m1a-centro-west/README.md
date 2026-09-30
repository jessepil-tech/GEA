---
title: SDD — M1a logos pack del centro
status: gate-done
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.maestros-m1a-centro-west
---

# SDD — M1a logos (`maestros-m1a-centro-west`)

`phase_id:` **`sdd.hospital.maestros-m1a-centro-west`**  
Padre: [`maestros-m1a-centro/`](../maestros-m1a-centro/) · capa 3 [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/)

El slug se reabre como **primer slice west**: `logoCentroAte` / `ts.pack_logos`. El shell west completo supera el techo (22 xhtml); el resto va a [`maestros-m1a-centro-west-resto/`](../maestros-m1a-centro-west-resto/).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Clarify · universo |
| [plan.md](plan.md) | Orden |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | dump `pack_logos` |
| [verify-report.md](verify-report.md) | **PASS / gate-done** 2026-09-23 · `id=1` pack · Playwright diferido |

**No es:** resto de hojas west (sector/caja/ambientes/mensajes), `servicioPorCentro` (M1b), ABM datos centro (padre gate-done), auditoría `AUD_CENTRO_ATENCION`, pack en Empresa / PtoVta / Param HC, M2.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7–8** · **gate-done** 2026-09-23 |
| Reservas | BODY n/a · Flyway n/a · writer `pack_logos` + `centro_atencion.id_pack_logos` cobrado |
| Universo firmado | inventarios G0 de este slug (logo) |
| Fixture | padres `centro_atencion` dump `id=1001`/`id=1002` · pack CU `id=1` |
| Evidencia | 8 verificado · 0 no verificado · 1 no ejecutado (Playwright) |
| Diferidos abiertos | resto west [`maestros-m1a-centro-west-resto`](../maestros-m1a-centro-west-resto/) · Playwright `diferido(fixture)` · NFR `diferido(perf-volumen)` · `diferido(multi-instalacion)` |
| Próximo paso | Cola corta: no abrir resto west sin gatillo sector/caja/mensajes |

Instalación: **TS** genérica · `diferido(multi-instalacion)`.

Jobs: `--jobs logo` coincidió ANMAT (no es este circuito) → **N/A**. `--jobs centro_atencion` vacío → **N/A**.

Auditoría: **N/A** — dump sin `TBL_AUD_PACK*`. Sello `fecha_last_update` / `actualizado_por` = último cambio.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Maestros | este workspace |
| BODY / package | **n/a** — JDBC/`NextIdService` PACK_LOGOS | libre |
| Rango Flyway | **n/a** | n/a |
| Tablas `ts` que escribe | `pack_logos`; FK `centro_atencion.id_pack_logos` | writer de este corte |
| Rama | al implementar | — |

No reabre ABM datos del padre. No escribe Empresa/PtoVta/Param HC.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.centro_atencion` | **no** | dump M1a | `id=1001` `id=1002` |
| `ts.pack_logos` | **sí** (CU logos) | PUT dump | no seedear filas |
| `ts.sec_id_tabla` (`PACK_LOGOS`) | no (contador) | seed `scripts/sql/seeds/sec-id/pack_logos.sql` | aplicar dump |
