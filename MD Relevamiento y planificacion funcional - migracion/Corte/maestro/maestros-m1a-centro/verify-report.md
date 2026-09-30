---
title: Verify — M1a ABM centro de atención
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro.verify
---

# Verify — `maestros-m1a-centro`

**Gate:** **PASS / gate-done** 2026-09-18. `verificar-sdd.sh maestros-m1a-centro`
FAIL 0. Escritura dump **id_centro_ate=1002** vigente (SELECT + GET 200).
Playwright **diferido(fixture)**. NFR **diferido(perf-volumen)**.
Hijos: grp · west · [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Alta/edición/baja centro | **done** · alta dump `id=1002` · edición/baja IT (borra la fila) | RF-1–3 · ledger #3 #5 |
| Buscar + dialog | **done** · dialog HIS absorbido por listado HAB | inventario interacción · ledger #3 |
| Combos grp/provincia/localidad | GET en Resource · ABM padres diferido | `maestros-m1a-grp` · M2 |
| Hojas west | diferido | `maestros-m1a-centro-west` |
| Servicio por centro | diferido | `maestros-m1b-servicio` |
| Auditoría campo a campo | diferido | [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/) |
| Jobs | N/A | `--jobs centro_atencion` sin hits |

## Paridad UI (xhtml)

| Artefacto G0 | Estado |
|--------------|--------|
| [inventario-copy-msg.md](inventario-copy-msg.md) | hecho 2026-09-16 |
| [inventario-validaciones.md](inventario-validaciones.md) | hecho 2026-09-16 |
| [inventario-geometria.md](inventario-geometria.md) | hecho 2026-09-16 |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | hecho 2026-09-16 |
| Template Angular | `centro-atencion-abm` listado HAB + dialog datos · smoke Agregar dump **id=1002** 2026-09-17 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (`/configuracion/centros-atencion`) |
| Decisión | **diferido(fixture)** |
| Viaje | — |
| Fixture | seed padres 99001 + `sec_id_tabla.CENTRO_ATENCION` + alta CU |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | Universo semilla `centroAtencion` techo ok | N/A | `indice-legacy.sh --semilla centroAtencion` · 4 xhtml / 3 beans | 2026-09-16 | verificado |
| 2 | Inventarios G0 copy/validaciones/geometría/interacción | geometría / copy | paths de este slug | 2026-09-16 | verificado |
| 3 | POST crea `ts.centro_atencion` vigente en dump | escritura | `id_centro_ate=1002` `M1A-P6-DUMP` / `M1A P6 evidencia dump` · `SELECT id_centro_ate, centro_atencion, nombre_centro FROM ts.centro_atencion WHERE id_centro_ate=1002` · CU Agregar Web `/configuracion/centros-atencion` · dump `grupogea-hospital_dev` | 2026-09-17 | verificado |
| 4 | Endpoint alta | endpoint | `POST /api/v1/configuracion/centros-atencion` status **201** · cuerpo `idCentroAte=1004` (conc B) · CQRS `CreateCentroAtencionCommand` success 341 ms (alta UI 1002, 15:52:43.698–15:52:44.039) | 2026-09-17 | verificado |
| 5 | IT Api | test | `mvn test -pl presentation-api -am -Dtest=CentrosAtencionResourceIT` · **Tests run: 4, Failures: 0** · `CentroAtencionRulesTest` PASS · `ng test` use-case 1 PASS | 2026-09-17 | verificado |
| 6 | Actor sin tile 10203 no ve la entrada | acceso | Identity `m1a_sintile` / `user_role` / JWT `Permission=RECEPCION` (sin `ADMINISTRACION_GENERAL_NA`) · `menuShowAll=false` · dashboard `data-testid=modules-grid` texto **solo** `RECEPCIÓN` · sidebar sin módulo AG · deep link `/configuracion/centros-atencion` **sí carga** (ruta solo `isAuthenticatedGuard`; BB HIS no aborta `rol_funcional_pers` en insert) | 2026-09-17 | verificado |
| 7 | NFR tiempo / volumen / numerador | no funcional | `COUNT(*)=1` antes del CU (dump, no orden Oracle) → `medido en vacío` + **`diferido(perf-volumen)`** · escritura UI 341 ms (n=1, no p95) · `GET` list 72 ms n=4 · listado pagina **en memoria** (`allRows`) · dos POST paralelos PK `1003` y `1004` distintos (`sec_id_tabla.CENTRO_ATENCION` UPDATE de fila; un 409 `El registro ya existe` no fue colisión de PK) | 2026-09-17 | verificado |
| 8 | Viaje Playwright | e2e | decisión `diferido(fixture)` · no corrido | spec Clarify #8 | no ejecutado |
| 9 | Sidecar BIRT | N/A | este corte no emite reporte (Clarify #5: sin cola/TV) · **0 kb** | spec | verificado |
| 10 | GET id 1002 vigente al cerrar | endpoint | `GET /api/v1/configuracion/centros-atencion/1002` **200** · dump `SELECT … WHERE id_centro_ate=1002` → `M1A-P6-DUMP` / `activo=S` | 2026-09-18 | verificado |
