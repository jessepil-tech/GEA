---
title: Verify — M1a grp
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.verify
---

# Verify — `maestros-m1a-grp`

**Gate:** **PASS / gate-done** 2026-09-18. Escritura dump `id=99002` y `id=99003`.
Playwright **diferido(fixture)**. NFR **diferido(perf-volumen)**.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| ABM `grp_centro_atencion` 10217 | **done** · dump `id=99002` / `id=99003` | spec RF-G1 · chrome HAB |
| Combo GET M1a | ya portado | `ListGrpCentroAtencionQuery` |
| Jobs | N/A | `--jobs grp_centro` vacío |
| Auditoría campo a campo | N/A | sin `TBL_AUD_GRP*CENTRO*` |

## Paridad UI

| Artefacto G0 | Estado |
|--------------|--------|
| copy / validaciones | hecho 2026-09-18 |
| geometría layoutPane HIS | **N/A** — HAB |
| north 500px · leyenda · checkbox default S | hecho · HAB Agregar 2026-09-18 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla? | sí (`/configuracion/grupos-centro-atencion`) |
| Decisión | **diferido(fixture)** |
| Fixture | alta nombre + checkbox default S |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | Semilla `grpCentroAtencion` techo ok | N/A | índice 3 xhtml / 2 beans · techo ok | 2026-09-18 | verificado |
| 2 | Jobs | N/A | `--jobs grp_centro` vacío | 2026-09-18 | verificado |
| 3 | G0 inventarios | geometría / copy | este slug | 2026-09-18 | verificado |
| 4 | INSERT `ts.grp_centro_atencion` | escritura | `id=99002` · POST `/api/v1/configuracion/grupos-centro-atencion` · dump `grupogea-hospital_dev` · `SELECT id_grp_centro_ate, grp_centro_ate, permite_elegir_centro_turnos FROM ts.grp_centro_atencion WHERE id_grp_centro_ate=99002` → `(99002, M1A-GRP-DUMP, S)` | 2026-09-18 | verificado |
| 5 | Actor sin tile 10217 no ve la entrada | acceso | Identity `m1a_sintile` / JWT `Permission=RECEPCION` · **sin el rol AG** · `menuShowAll=false` · dashboard tiles **solo** `RECEPCIÓN` · sidebar `Inicio` + `RECEPCIÓN` · deep link `/configuracion/grupos-centro-atencion` **sí carga** (ruta `isAuthenticatedGuard`; BB HIS no aborta `rol_funcional_pers`) | 2026-09-18 | verificado |
| 6 | NFR tiempo / volumen / NextId | no funcional | `COUNT=4` (pre-CU=2 demo+fixture) → **`diferido(perf-volumen)`** · POST **272 ms** · GET list warm **305 ms** n=3 · vacío nombre **400** · duplicado nombre **409** · sin `FOR UPDATE` en el bean | 2026-09-18 | verificado |
| 7 | Viaje Playwright | e2e | decisión `diferido(fixture)` · no corrido | spec Clarify #8 | no ejecutado |
| 8 | INSERT HAB Agregar | escritura | `id=99003` M1A-GRP-UI · CU Agregar · `SELECT … WHERE id_grp_centro_ate=99003` → `(99003, M1A-GRP-UI, S)` | 2026-09-18 | verificado |
| 9 | `GrpCentroAteRulesTest` | build/test | `mvn -pl application -am test -Dtest=GrpCentroAteRulesTest` Failures: 0 | 2026-09-18 | verificado |
| 10 | GET list vigente al cerrar | endpoint | `GET /api/v1/configuracion/grupos-centro-atencion` **200** total=3 luego 4 | 2026-09-18 | verificado |
