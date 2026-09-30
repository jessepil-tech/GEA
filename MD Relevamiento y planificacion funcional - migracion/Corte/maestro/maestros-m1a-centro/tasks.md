---
title: Tasks — M1a ABM centro de atención
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro.tasks
---

# Tasks — M1a

## Loop (pasos 1–4)

- [x] Elegir fila backlog (M1a)
- [x] Reservar (BODY n/a · Flyway n/a · `ts.centro_atencion`)
- [x] Firmar universo (semilla `centroAtencion` + `datosCentroAtencion`; techo ok)
- [x] Contratar fixture (padres seed · centro = este CU)
- [x] Gate UI inventarios G0 (copy / validaciones / geometría / interacción)
- [x] Viaje Playwright declarado `diferido(fixture)`
- [x] Jobs: N/A `centro_atencion`; instalación TS; acceso menú

## Implementación (paso 5)

- [x] **TSK-app-g1-1** Puerto + JDBC `ts.centro_atencion` (columnas dump; NextId `CENTRO_ATENCION`)
- [x] **TSK-app-g1-2** Commands alta/edición/baja + queries list/get + combos grp/provincia/localidad/servicios-dflt
- [x] **TSK-app-g1-3** Resource `/api/v1/configuracion/centros-atencion` + `CentrosAtencionResourceIT` **4 PASS**
- [x] **TSK-web-g2-1** Clases UI del slice (chrome HAB catálogo)
- [x] **TSK-web-g2-2** Template listado HAB + `*-labels.ts` + ruta `/configuracion/centros-atencion` + hoja menú AG
- [x] **TSK-web-g2-3** Buscador absorbido por el listado · dialog datos `closeOnBackdrop=false`
- [x] Apply seed padres con `psql` en la PG **viva** del Api (ids 99001 ya presentes; `ON CONFLICT DO NOTHING`)
- [x] **No** `Vnn` Flyway

## Evidencia (paso 6)

- [x] Ledger: escritura dump **id=1002** · POST 201 · actor sin tile 10203 · NFR `diferido(perf-volumen)`
- [x] NFR: `COUNT=1` dump (vacío vs Oracle) + escritura 341 ms n=1 + conc PK 1003/1004 · **no** p95
- [x] Paso 7 `verificar-sdd.sh` FAIL 0 / gate PASS 2026-09-18 (Playwright sigue `diferido(fixture)`)
