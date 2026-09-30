---
title: Verify — especialidad_serv
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.verify
---

# Verify — `maestros-m1c-especialidad-serv`

**Gate:** **PASS / gate-done** 2026-09-18. Escritura dump triple
`id=1002` / `id=11` / `id=1` y `id=2`. Playwright **diferido(fixture)**.
NFR **medido en vacío** + **diferido(perf-volumen)**.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| ABM `especialidad_serv` | **done** · dump `(1002,11,1)` y `(1002,11,2)` | spec RF-ES1 · chrome HAB |
| Tabs west servicio-centro | diferido | [`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/) |
| Jobs | N/A | `--jobs especialidad_serv` vacío |
| Auditoría campo a campo | N/A | sin `TBL_AUD_ESPEC*SERV*` |

## Paridad UI

| Artefacto G0 | Estado |
|--------------|--------|
| copy / validaciones | hecho 2026-09-18 |
| geometría layoutPane HIS | **N/A** — HAB |
| dialog 600px · label 100px | hecho · HAB Agregar 2026-09-18 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla? | sí (`/configuracion/especialidades-servicio`) |
| Decisión | **diferido(fixture)** |
| Fixture | alta triple con padres 1002 / 11 / 1 y / 2 |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | Semilla `especialidadServ` techo ok | N/A | índice 2 xhtml / 2 beans | 2026-09-18 | verificado |
| 2 | Jobs | N/A | `--jobs especialidad_serv` vacío | 2026-09-18 | verificado |
| 3 | G0 inventarios | geometría / copy | este slug | 2026-09-18 | verificado |
| 4 | INSERT `ts.especialidad_serv` | escritura | `id=1002` `id=11` `id=1` triple `(id_centro_ate=1002, id_servicio=11, id_especialidad=1)` · POST `/api/v1/configuracion/especialidades-servicio` · dump `grupogea-hospital_dev` · `SELECT id_centro_ate, id_servicio, id_especialidad, mensaje_espera_serv_centro FROM ts.especialidad_serv WHERE id_centro_ate=1002 AND id_servicio=11 AND id_especialidad=1` → `(1002, 11, 1, M1C-SERV-DUMP)` | 2026-09-18 | verificado |
| 5 | Actor sin tile 10204 no ve la entrada | acceso | Identity `m1a_sintile` / `user_role` / JWT `Permission=RECEPCION` (sin `ADMINISTRACION_GENERAL_NA`) · **sin el rol** · `menuShowAll=false` · dashboard `data-testid=modules-grid` texto **solo** `RECEPCIÓN` · sidebar `Inicio` + `RECEPCIÓN` · deep link `/configuracion/especialidades-servicio` **sí carga** (ruta solo `isAuthenticatedGuard`; BB HIS no aborta `rol_funcional_pers`) | 2026-09-18 | verificado |
| 6 | NFR tiempo / volumen / PK | no funcional | `COUNT(especialidad_serv)=2` dump (antes del CU = 0) → `medido en vacío` + **`diferido(perf-volumen)`** · POST **797 ms** · GET list **266 ms** n=2 · GET `1002/11/1` **65 ms** · GET `1002/11/2` **47 ms** · duplicado 23505 → **409** · sin `FOR UPDATE` en el bean | 2026-09-18 | verificado |
| 7 | Viaje Playwright | e2e | decisión `diferido(fixture)` · no corrido | spec Clarify #8 | no ejecutado |
| 8 | INSERT HAB Agregar | escritura | `id=1002` `id=11` `id=2` PEDIATRIA M1C-UI · CU Agregar · `SELECT … WHERE id_especialidad=2` → `(1002, 11, 2, M1C-SERV-UI)` | 2026-09-18 | verificado |
| 9 | `EspecialidadServRulesTest` | build/test | `mvn -pl core,application -am test -Dtest=EspecialidadServRulesTest` Tests run: 5 Failures: 0 | 2026-09-18 | verificado |
| 10 | GET triple vigente al cerrar | endpoint | `GET /api/v1/configuracion/especialidades-servicio/1002/11/1` **200** · dump fila `M1C-SERV-DUMP` | 2026-09-18 | verificado |
