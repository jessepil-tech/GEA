---
title: SDD — M1a ABM centro de atención
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro
---

# SDD — M1a ABM centro de atención (`maestros-m1a-centro`)

`phase_id:` **`sdd.hospital.maestros-m1a-centro`**  
Padre capa 3: [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/) (M1 partido)  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md)

Producto (2026-09-16): primer writer de maestros territoriales. **No** servicio suelto.
Hijo inmediato: [`maestros-m1b-servicio/`](../maestros-m1b-servicio/).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Problema, Clarify, RF, universo firmado |
| [plan.md](plan.md) | Capas starter · Gate UI · sin Flyway |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría + menú |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Shape `ts.centro_atencion` vs dump |
| [verify-report.md](verify-report.md) | **PASS / gate-done** 2026-09-18 · dump id=1002 · Playwright diferido |

**No es:** ABM servicio, `servicio_centro`, especialidad, grp 10217, hojas west (logo/sectores/caja), P3, T6.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7–8** · **gate-done** 2026-09-18 |
| Reservas | BODY n/a · Flyway n/a · writer `centro_atencion` cobrado |
| Universo firmado | inventarios G0 de este slug |
| Fixture | resuelto · id `1002` vigente dump |
| Evidencia | 9 verificado · 0 no verificado · 1 no ejecutado (Playwright) |
| Diferidos abiertos | [`maestros-m1a-centro-west`](../maestros-m1a-centro-west/) **gate-done** (logos) · [`maestros-m1a-centro-west-resto`](../maestros-m1a-centro-west-resto/) · [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/) · Playwright `diferido(fixture)` · NFR `diferido(perf-volumen)` · `diferido(multi-instalacion)` · grp **gate-done** [`maestros-m1a-grp`](../maestros-m1a-grp/) |
| Próximo paso | Tronco M1 cobrado (M1c **gate-done**) |

Universo: shell + datos + buscador (absorbido por el listado HAB).
Instalación: **genérica (`cliente="TS"`)** · `diferido(multi-instalacion)`.

El A–C declara pipeline **cerrado** y el resto **muestra**; este corte **no hereda**
esa profundidad. Universo propio: jobs N/A · BODY n/a · FK padres contratadas por seed ·
BIRT N/A · writers de `centro_atencion` = este CU.

Jobs del circuito: ninguno con nombre `centro_atencion`. `CheckHabTurnosJob` lee `servicio_centro` → **N/A este corte** (M1b / jobs ya diferidos).

Auditoría: `AUD_CENTRO_ATENCION` en dump → [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/) (`TBL_AUD_*` no portado).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Maestros | este workspace |
| BODY / package | **n/a** — JDBC/`NextIdService` sobre `ts.*`; no se porta BODY PERSONAS/GENERAL | libre |
| Rango Flyway | **n/a** — `centro_atencion` está en el dump. Prohibido `Vnn` seed o `CREATE TABLE ts.*` | n/a |
| Tablas `ts` que escribe | `centro_atencion` | writer de este corte |
| Rama | al implementar (Api + Web) | — |

No pisa `ts.turno` ni el BODY TURNOS. M1b no escribe `centro_atencion`.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.centro_atencion` | **sí** (es el ABM) | CU | **id vigente** `id_centro_ate=1002` (Agregar Web dump 2026-09-17). IT crea y borra en Testcontainers |
| `ts.grp_centro_atencion` | **no** | seed `#3` `Hospital-Api/infrastructure/src/main/resources/db/dev-seed/ts_maestros_m1a_centro_padres.sql` · ABM grp → [`maestros-m1a-grp`](../maestros-m1a-grp/) | contratado (script; apply `psql`) |
| `ts.provincia` / `ts.localidad` | **no** | mismo seed · ABM → M2 | contratado |
| `ts.sec_id_tabla` (`CENTRO_ATENCION`) | **no** (contador) | mismo seed · el dump no traía la fila | contratado 2026-09-17 (apply `psql`; no Flyway) |
| Depósito / server mail-sms / servicios dflt | **no** | lupas west/datos | `diferido(maestros-m1a-centro-west)` |
| Paciente / personal | **no** | N/A este CU | — |

Evidencia de paridad = **id** de fila nacido del CU en `ts.centro_atencion`, no el seed de grp.
