---
title: Verify — T1 Turnos maestros identidad
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-maestros-personal
---

# Verify — T1

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Gate operador call center (`BBInicioTurnos`) | **done** (API + UI picker) | `GET /api/v1/turnos/inicio` · `/turnos/inicio` |
| Catálogo call center | **DDL + GET vía inicio**; ABM diferido | `catalogo-abm-call-center` |
| Personal hospitalario | **DDL + lookup login**; ABM diferido | `catalogo-abm-personal` |
| Vínculo personal↔call center | **DDL + seed + GET** | — |
| Admin turnos serv/centro | **DDL**; ABM/UI diferido T2 | — |
| Motivos / tipo_motivo_df | **DDL + seed + GET** | `catalogo-abm-motivo` |
| Menú ATENCION_TURNOS | **parcial** (hoja Web); filtro Identity diferido | `identidad-menus-m2` |
| Agenda / otorgar | diferido | T5 |

## Criterios

| CA | Resultado |
|----|-----------|
| 1 Flyway columnas 13/10/4/9/3/5 | **PASS** — V34 alineado a `pg_ts_columns.csv` |
| 2 IT con/sin vínculo / 401 | **IT escrito**; ejecución bloqueada sin Docker Dev Services (mismo entorno que ITs Api previas) |
| 3 UI picker + tile | **PASS** (código Web) |
| 4 Sin regresión AGI/colas | Sin cambios en adapters AGI/recepción/anunciador |
| 5 Sin silencios en inventario | Tabla arriba completa |

## Smoke host (al levantar Api)

**PASS 2026-08-27** (stack local Identity:8080 Api:8081 Web:4200):

1. Flyway V34+V35 aplicado (Api ya arriba).
2. Login `admin` / `Admin123!` → 200.
3. `GET /api/v1/turnos/inicio` → `loginName=admin`, CC 92001/92002.
4. `GET /api/v1/turnos/motivos` → seed SUSPENSION/SOBRE/REEMPLAZO.
5. UI: menú TURNOS → `/turnos/inicio` (picker).

Plantilla:

1. `mvn … quarkus:dev` Hospital-Api con `HOSPITAL_PG_*` → Flyway aplica **V34 + V35**.
2. Login Web `admin` / `Admin123!` (D-TUR-01: `login_name=admin`).
3. Menú TURNOS → `/turnos/inicio` → lista Call Center Demo / Norte.
4. `GET /api/v1/turnos/inicio` (Bearer) → `personal.loginName=admin`, `callCenters` ≥ 2.
5. Usuario sin vínculo → 404 `USUARIO_SIN_CALL_CENTER` / personal.

## RF-3

FKs `ts.turno` → `personal` / `call_center` / `motivo` **no** habilitadas en T1 (seed AGI OTORGADO). Siguen en [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md).

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (picker `/turnos/inicio`) |
| Decisión | **cerrado-pre-regla** |
| Viaje (pasos) | smoke host 2026-08-27 (gate parcial) |
| Fixture | `admin` / CC 92001–92002 |
| Legacy e2e | no |
