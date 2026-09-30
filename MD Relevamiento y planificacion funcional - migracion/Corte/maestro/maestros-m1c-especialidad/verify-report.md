---
title: Verify — M1c especialidad
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad.verify
---

# Verify — `maestros-m1c-especialidad`

**Gate:** **PASS / gate-done** 2026-09-18. Escritura dump **id=1** y **id=2**
vigentes. Playwright **diferido(fixture)**. NFR **diferido(perf-volumen)**.
Hijo: [`maestros-m1c-especialidad-serv`](../maestros-m1c-especialidad-serv/).
Auditoría campo a campo **N/A** (dump sin `aud_especialidad`; Legacy-DB sin
`TBL_AUD_ESPEC*`).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| ABM especialidad | **done** · dump `id=1` y `id=2` | spec RF-E1 · chrome HAB |
| `especialidad_serv` | **done** | [`maestros-m1c-especialidad-serv`](../maestros-m1c-especialidad-serv/) **gate-done** 2026-09-18 |
| Especialidad quirúrgica | N/A | otro dominio (fuera 10003) |
| Jobs | N/A | `--jobs especialidad` vacío |
| Auditoría campo a campo | N/A | no hay `AUD_ESPECIALIDAD` / `TBL_AUD_ESPEC*` |

## Paridad UI

| Artefacto G0 | Estado |
|--------------|--------|
| copy / validaciones | hecho 2026-09-17 |
| geometría layoutPane HIS | **N/A** — HAB |
| campo 245px dialog | hecho |
| Template listado HAB | hecho 2026-09-17 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (`/configuracion/especialidades`) |
| Decisión | **diferido(fixture)** |
| Viaje | — |
| Fixture | alta especialidad CU |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | Semilla `especialidad` techo ok | N/A | índice 3 xhtml / 2 beans | 2026-09-17 | verificado |
| 2 | Jobs | N/A | `--jobs especialidad` vacío | 2026-09-17 | verificado |
| 3 | G0 inventarios | geometría / copy | este slug | 2026-09-17 | verificado |
| 4 | INSERT `ts.especialidad` | escritura | `id=1` `id_especialidad=1` CARDIOLOGIA M1C-DUMP · POST `/api/v1/configuracion/especialidades` · dump `grupogea-hospital_dev` · `SELECT id_especialidad, especialidad, interconsulta, adulto_pediatrico FROM ts.especialidad WHERE id_especialidad=1` → `(1, CARDIOLOGIA M1C-DUMP, N, TODOS)` | 2026-09-17 | verificado |
| 5 | Actor sin tile 10003 no ve la entrada | acceso | Identity `m1a_sintile` / `user_role` / JWT `Permission=RECEPCION` (sin `ADMINISTRACION_GENERAL_NA`) · `menuShowAll=false` · dashboard `data-testid=modules-grid` texto **solo** `RECEPCIÓN` · sidebar `Inicio` + `RECEPCIÓN` · deep link `/configuracion/especialidades` **sí carga** (CARDIOLOGIA M1C-DUMP / PEDIATRIA M1C-UI / NUEVO; ruta solo `isAuthenticatedGuard`; BB HIS no aborta `rol_funcional_pers`) | 2026-09-18 | verificado |
| 6 | NFR tiempo / volumen / numerador | no funcional | `COUNT(especialidad)=3` dump (ids 1, 2, 3; no orden Oracle) → `medido en vacío` + **`diferido(perf-volumen)`** · GET list **535 ms** n=3 · GET `especialidades/1` **62 ms** · GET `/2` **94 ms** · NextId `ESPECIALIDAD` (sin `FOR UPDATE` en el bean) | 2026-09-18 | verificado |
| 7 | Viaje Playwright | e2e | decisión `diferido(fixture)` · no corrido | spec Clarify #8 | no ejecutado |
| 8 | INSERT HAB Agregar | escritura | `id=2` `id_especialidad=2` PEDIATRIA M1C-UI · CU Agregar · `SELECT id_especialidad, especialidad FROM ts.especialidad WHERE id_especialidad=2` | 2026-09-17 | verificado |
| 9 | `EspecialidadRulesTest` | build/test | `mvn test -pl application -Dtest=EspecialidadRulesTest` exit 0 | 2026-09-17 | verificado |
| 10 | GET id 1 vigente al cerrar | endpoint | `GET /api/v1/configuracion/especialidades/1` **200** · dump `SELECT … WHERE id_especialidad=1` → `CARDIOLOGIA M1C-DUMP` | 2026-09-18 | verificado |
