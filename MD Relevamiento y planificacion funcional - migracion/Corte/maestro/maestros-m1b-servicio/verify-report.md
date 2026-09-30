---
title: Verify — M1b servicio + vínculo
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1b-servicio.verify
---

# Verify — `maestros-m1b-servicio`

**Gate:** **PASS / gate-done** 2026-09-18. Escritura dump **id=11** y par
`(1002, 11)` vigentes. Playwright **diferido(fixture)**. NFR
**diferido(perf-volumen)**. Hijos: tabs · [`maestros-m1b-auditoria-servicio`](../maestros-m1b-auditoria-servicio/). M1c **gate-done**.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| ABM servicio | **done** · dump `id=11` | spec RF-S1 · chrome HAB |
| ABM vínculo datos | **done** · par `(1002, 11)` | spec RF-S2 |
| Tabs amb/int/lab | diferido | [`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/) |
| Especialidad | **gate-done** | [`maestros-m1c-especialidad`](../maestros-m1c-especialidad/) |
| `CheckHabTurnosJob` | diferido | [`relevamiento-procesos-programados`](../../../relevamiento/relevamiento-procesos-programados/) |
| `InsertarDeterminacionServicioJob` | N/A | Lab |
| Auditoría campo a campo | diferido | [`maestros-m1b-auditoria-servicio`](../maestros-m1b-auditoria-servicio/) |

## Paridad UI

| Artefacto G0 | Estado |
|--------------|--------|
| copy / validaciones | hecho 2026-09-16 |
| geometría layoutPane HIS | **N/A** — firma 2026-09-17b listado HAB |
| campo servicio 245px (dialog) | hecho |
| Template listado HAB catálogo | hecho |
| Template listado HAB vínculo | hecho 2026-09-17 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (`/configuracion/servicios` · `/configuracion/servicios-centro`) |
| Decisión | **diferido(fixture)** |
| Viaje | — |
| Fixture | alta servicio + alta vínculo (par) |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | Semilla `servicio` techo ok | N/A | `indice-legacy.sh --semilla servicio` | 2026-09-16 | verificado |
| 2 | Semilla path `servicioCentro/servicioCentro` techo ok | N/A | índice 3 xhtml / 2 beans | 2026-09-16 | verificado |
| 3 | G0 inventarios | geometría / copy | paths de este slug | 2026-09-16 | verificado |
| 4 | INSERT `ts.servicio` | escritura | `id=11` `id_servicio=11` M1B-P6-DUMP · CU Crear servicio · dump `grupogea-hospital_dev` · `SELECT id_servicio, servicio FROM ts.servicio WHERE id_servicio=11` | 2026-09-17 | verificado |
| 5 | INSERT `ts.servicio_centro` | escritura | `id=11` par `(id_centro_ate=1002, id_servicio=11)` · CU Crear servicio centro · dump · `SELECT id_centro_ate, id_servicio, amb_int_todos, tipo_servicio, activo, atiende_turnos FROM ts.servicio_centro WHERE id_centro_ate=1002 AND id_servicio=11` → `(1002, 11, TODOS, ATENCION_MEDICA, S, N)` | 2026-09-17 | verificado |
| 6 | Endpoints | endpoint | `POST /api/v1/configuracion/servicios` → id 11 · `POST /api/v1/configuracion/servicios-centro` 201 251 ms · par 1002/11 · duplicate 409 | 2026-09-17 | verificado |
| 7 | Actor sin tile 10002/10204 no ve la entrada | acceso | Identity `m1a_sintile` / `user_role` / JWT `Permission=RECEPCION` (sin `ADMINISTRACION_GENERAL_NA`) · `menuShowAll=false` · dashboard `data-testid=modules-grid` texto **solo** `RECEPCIÓN` · sidebar sin módulo AG · deep link `/configuracion/servicios` y `/configuracion/servicios-centro` **sí cargan** (ruta solo `isAuthenticatedGuard`; BB HIS no aborta `rol_funcional_pers`) | 2026-09-18 | verificado |
| 8 | NFR tiempo / volumen / par PK | no funcional | `COUNT(servicio)=3` `COUNT(servicio_centro)=2` dump (no orden Oracle) → `medido en vacío` + **`diferido(perf-volumen)`** · GET list servicios **366 ms** n=3 · GET `servicios/11` **69 ms** · escritura vínculo 251 ms n=1 (no p95) · duplicate par **409** | 2026-09-18 | verificado |
| 9 | Viaje Playwright | e2e | decisión `diferido(fixture)` · no corrido | spec Clarify #8 | no ejecutado |
| 10 | GET id 11 vigente al cerrar | endpoint | `GET /api/v1/configuracion/servicios/11` **200** · dump `SELECT … WHERE id_servicio=11` → `M1B-P6-DUMP` | 2026-09-18 | verificado |
