---
title: SDD — vínculo especialidad por servicio-centro
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv
---

# SDD — `maestros-m1c-especialidad-serv`

`phase_id:` **`sdd.hospital.maestros-m1c-especialidad-serv`**  
Padre: [`maestros-m1c-especialidad/`](../maestros-m1c-especialidad/) · capa 3 [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/)

Hoja west `especialidadServ.xhtml` (10204) como listado HAB. PK dump
`(id_centro_ate, id_servicio, id_especialidad)`.

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Clarify · universo |
| [plan.md](plan.md) | Orden |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | dump `especialidad_serv` |
| [verify-report.md](verify-report.md) | Ledger |

**No es:** catálogo `especialidad` (M1c), hoja datos `servicio_centro` (M1b), tabs amb/int/lab
([`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/)), quirúrgica, west de centro.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7–8** · **gate-done** 2026-09-18 |
| Reservas | BODY n/a · Flyway n/a · writer `especialidad_serv` cobrado |
| Universo firmado | inventarios G0 de este slug |
| Fixture | resuelto · triples `(1002,11,1)` y `(1002,11,2)` vigentes dump |
| Evidencia | 9 verificado · 0 no verificado · 1 no ejecutado (Playwright) |
| Diferidos | Playwright `diferido(fixture)` · NFR `diferido(perf-volumen)` · `diferido(multi-instalacion)` · buscador absorbido |
| Próximo paso | Cola corta cobrada · no abrir M2 en este carril |

Instalación: **TS** genérica · `diferido(multi-instalacion)`.  
Jobs: `--jobs especialidad_serv` vacío → **N/A**.  
Auditoría campo a campo: **N/A** (sin `TBL_AUD_ESPEC*SERV*` / `aud_especialidad_serv`).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Maestros | este workspace |
| BODY / package | **n/a** — JDBC; no BODY PERSONAS | libre |
| Rango Flyway | **n/a** | n/a |
| Tablas `ts` que escribe | `especialidad_serv` | writer de este corte |
| Rama | al implementar | — |

No escribe `especialidad`, `servicio`, `servicio_centro` ni `centro_atencion`.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.especialidad_serv` | **sí** (ABM hoja 10204) | CU dump | vigente `(1002,11,1)` y `(1002,11,2)` |
| `ts.centro_atencion` | no | M1a `id=1002` | vigente |
| `ts.servicio` | no | M1b `id=11` | vigente |
| `ts.especialidad` | no | M1c `id=1` / `id=2` | vigente |
