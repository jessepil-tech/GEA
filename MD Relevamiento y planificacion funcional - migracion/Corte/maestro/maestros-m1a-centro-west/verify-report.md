---
title: Verify — M1a logos
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.maestros-m1a-centro-west.verify
---

# Verify — `maestros-m1a-centro-west`

**Gate:** **PASS / gate-done** 2026-09-23. Playwright **diferido(fixture)**. NFR **diferido(perf-volumen)**.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Pack logos centro (`logoCentroAte`) | **done** dump `id=1` · west en edición | spec RF-L1 |
| Resto west | diferido | [`maestros-m1a-centro-west-resto`](../maestros-m1a-centro-west-resto/) |
| `servicioPorCentro` | N/A | M1b |
| Jobs | N/A | `--jobs logo` ANMAT |
| Auditoría campo a campo | N/A | sin `TBL_AUD_PACK*` |

## Paridad UI

| Artefacto G0 | Estado |
|--------------|--------|
| copy / validaciones | hecho 2026-09-18 |
| geometría layoutPane HIS | west 170px **dentro** de la ficha (`host=page`, `app-his-west-accordion`) · listado HAB |
| preview h=32 · 7 filas · Aceptar | main de la ficha · `centro-atencion-logos-table` |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla? | sí (Editar → west Logo en `/configuracion/centros-atencion`) |
| Decisión | **diferido(fixture)** |
| Fixture | SVG `logo-app` sobre centro dump existente |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | Semilla `logoCentroAte` techo ok | N/A | índice 2 xhtml / 2 beans · techo ok | 2026-09-18 | verificado |
| 2 | Jobs | N/A | `--jobs logo` ANMAT (N/A este circuito) | 2026-09-18 | verificado |
| 3 | G0 inventarios | geometría / copy | este slug | 2026-09-18 | verificado |
| 4 | INSERT `ts.pack_logos` + FK centro | escritura | `id=1` · PUT `/api/v1/configuracion/centros-atencion/1002/logos/logo-app` · dump `grupogea-hospital_dev` · `SELECT p.id_pack_logos, length(p.logo_app), c.id_centro_ate FROM ts.pack_logos p JOIN ts.centro_atencion c ON c.id_pack_logos=p.id_pack_logos WHERE p.id_pack_logos=1` → `(1, 69, 1002)` | 2026-09-18 | verificado |
| 5 | Actor sin tile 10203 no ve la entrada | acceso | Identity `m1a_sintile` / JWT `Permission=RECEPCION` · **sin el rol AG** · `menuShowAll=false` · dashboard `data-testid=modules-grid` texto **solo** `RECEPCIÓN` · sidebar `Inicio` + `RECEPCIÓN` · deep link `/configuracion/centros-atencion` **sí carga** (ruta `isAuthenticatedGuard`; BB HIS no aborta `rol_funcional_pers`) | 2026-09-23 | verificado |
| 6 | NFR tiempo / volumen | no funcional | PUT **625 ms** · COUNT pack=1 · **`diferido(perf-volumen)`** · HIS sin `FOR UPDATE` | 2026-09-18 | verificado |
| 7 | Viaje Playwright | e2e | `diferido(fixture)` · no corrido | spec Clarify #8 | no ejecutado |
| 8 | HAB west Logo 7 filas + Aceptar | e2e | ops `admin` 2026-09-24 · listado **sin** west · Editar → `/configuracion/centros-atencion/19` (CEDIM/AMV) **0** `p-dialog` · ficha `centro-atencion-ficha-page` host=page · west Logo 7 slots + Aceptar (centro `17`) · Agregar → `/nuevo` Logo **disabled** · Cancelar/Volver → listado | 2026-09-24 | verificado |
| 9 | `PackLogosRulesTest` | build/test | `mvn -pl application -am test -Dtest=PackLogosRulesTest` Failures: 0 | 2026-09-18 | verificado |
