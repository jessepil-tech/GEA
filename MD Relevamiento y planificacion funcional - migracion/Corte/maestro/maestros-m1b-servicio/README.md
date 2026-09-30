---
title: SDD — M1b ABM servicio + servicio_centro
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1b-servicio
---

# SDD — M1b servicio + vínculo (`maestros-m1b-servicio`)

`phase_id:` **`sdd.hospital.maestros-m1b-servicio`**  
Padre capa 3: [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/)  
Prerrequisito catálogo: **ninguno**. Prerrequisito vínculo: [`maestros-m1a-centro/`](../maestros-m1a-centro/) (`id_centro_ate=1002` dump)

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Clarify · universo |
| [plan.md](plan.md) | Orden tras M1a |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | dump `servicio` / `servicio_centro` |
| [verify-report.md](verify-report.md) | **PASS / gate-done** 2026-09-18 · id=11 · par (1002, 11) · Playwright diferido |

**No es:** especialidad (M1c), tabs amb/int/lab del vínculo, ABM centro, Lab `InsertarDeterminacionServicioJob`, insert `param_atencion_serv`.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7–8** · **gate-done** 2026-09-18 |
| Reservas | BODY n/a · Flyway n/a · writers `servicio` / `servicio_centro` cobrados |
| Universo firmado | inventarios G0 de este slug |
| Fixture | resuelto · `id=11` y par `(1002, 11)` vigentes dump |
| Evidencia | 9 verificado · 0 no verificado · 1 no ejecutado (Playwright) |
| Diferidos abiertos | [`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/) · [`maestros-m1b-auditoria-servicio`](../maestros-m1b-auditoria-servicio/) · Playwright `diferido(fixture)` · NFR `diferido(perf-volumen)` · `diferido(multi-instalacion)` · jobs hab |
| Próximo paso | Tronco M1 cobrado (M1c **gate-done**) |

Instalación: **TS** genérica · `diferido(multi-instalacion)`.

El A–C declara pipeline **cerrado** y el resto **muestra**; este corte **no hereda**
esa profundidad. Universo propio: catálogo `servicio` + `servicio_centro` hoja
datos · jobs Lab N/A · `CheckHabTurnosJob` diferido(jobs) · `atiende_turnos` default `N`.

Jobs: `InsertarDeterminacionServicioJob` → **N/A** este ABM (Lab). `CheckHabTurnosJob` lee `servicio_centro.atiende_turnos` → `diferido(relevamiento-procesos-programados)` (ya en el programa).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Maestros | este workspace |
| BODY / package | **n/a** — JDBC/`NextIdService`; no BODY PERSONAS | libre |
| Rango Flyway | **n/a** | n/a |
| Tablas `ts` que escribe | `servicio` · `servicio_centro` | writer de este wave |
| Rama | al implementar | — |

No escribe `centro_atencion` ni `ts.turno`.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.servicio` | **sí** (ABM 10002) | CU dump id=11 | evidenciado |
| `ts.servicio_centro` | **sí** (ABM 10204) | CU dump par (1002, 11) | evidenciado |
| `ts.centro_atencion` | **no** | M1a CU id=1002 | padre vigente |
| Especialidad | **no** | [`maestros-m1c-especialidad`](../maestros-m1c-especialidad/) | **gate-done** |
| Tabs amb/int/lab | **no** | [`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/) | diferido |
| Auditoría campo a campo | **no** | [`maestros-m1b-auditoria-servicio`](../maestros-m1b-auditoria-servicio/) | diferido |

Evidencia catálogo = **id_servicio** nacido del CU. Evidencia vínculo = par PK nacido del CU, no seed.
