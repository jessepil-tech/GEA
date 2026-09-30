---
title: SDD — M1c ABM especialidad
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad
---

# SDD — M1c especialidad (`maestros-m1c-especialidad`)

`phase_id:` **`sdd.hospital.maestros-m1c-especialidad`**  
Padre capa 3: [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/)  
Prerrequisito catálogo: **ninguno** (tabla `ts.especialidad` sin FK de M1a/M1b).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Clarify · universo |
| [plan.md](plan.md) | Orden |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | dump `especialidad` |
| [verify-report.md](verify-report.md) | **PASS / gate-done** 2026-09-18 · id=1 · id=2 · Playwright diferido |

**No es:** `especialidad_serv` ([`maestros-m1c-especialidad-serv`](../maestros-m1c-especialidad-serv/)), especialidad quirúrgica, west de centro, consulta personal-especialidad.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7–8** · **gate-done** 2026-09-18 |
| Reservas | BODY n/a · Flyway n/a · writer `especialidad` cobrado |
| Universo firmado | inventarios G0 de este slug |
| Fixture | resuelto · `id=1` y `id=2` vigentes dump |
| Evidencia | 9 verificado · 0 no verificado · 1 no ejecutado (Playwright) |
| Diferidos abiertos | Playwright `diferido(fixture)` · NFR `diferido(perf-volumen)` · `diferido(multi-instalacion)` |
| Próximo paso | Tronco M1 cobrado · no abrir M2 en este carril |

Instalación: **TS** genérica · `diferido(multi-instalacion)`.

Jobs: `--jobs especialidad` sin coincidencias → **N/A**.

Auditoría: **N/A** — dump sin tabla `aud_especialidad`; Legacy-DB sin package `TBL_AUD_ESPEC*`.
Sello `fecha_last_update` / `actualizado_por` = último cambio.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Maestros | este workspace |
| BODY / package | **n/a** — JDBC/`NextIdService`; no BODY PERSONAS | libre |
| Rango Flyway | **n/a** | n/a |
| Tablas `ts` que escribe | `especialidad` | writer de este corte |
| Rama | al implementar | — |

No escribe `servicio`, `servicio_centro` ni `centro_atencion`.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.especialidad` | **sí** (ABM 10003) | CU dump | `id=1` y `id=2` vigentes |
| `ts.sec_id_tabla` (`ESPECIALIDAD`) | no (contador) | seed `db/dev-seed/ts_maestros_m1c_especialidad.sql` | aplicado dump 2026-09-17 |
| `especialidad_serv` | **no** | [`maestros-m1c-especialidad-serv`](../maestros-m1c-especialidad-serv/) | **gate-done** 2026-09-18 |
