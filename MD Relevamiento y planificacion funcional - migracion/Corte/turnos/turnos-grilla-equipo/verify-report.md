---
title: Verify — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-24
phase_id: sdd.hospital.turnos-grilla-equipo.verify
---

# Verify — Grilla en modo equipo

El índice, el viaje, la escritura, el acceso y la concurrencia están corridos. El tiempo de Generar supera el default y el volumen sigue diferido. **PASS / gate-done** 2026-09-24 con esos dos diferidos.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Generar con radio Equipo | done | ledger 2 y 5 |
| Eliminar con radio Equipo | done | ledger 3 |
| Consultar agendas por equipo | done | ledger 4 |
| Filtro Equipo de Agenda y hermanas | **diferido** | [`turnos-agenda-equipo`](../turnos-agenda-equipo/) |
| Horario especial en el cálculo | **diferido** | D-TUR-15 |
| Jobs laboratorio / ANMAT | **N/A** | no arman la grilla |
| Jobs de asientos, ocupación, honorarios y pedido | **N/A** | `--jobs generacion` |
| Jobs `grilla` y `agendas` | **N/A** | sin coincidencias |
| Auditoría campo a campo de `ts.turno` | **diferido(auditoria)** | existe `TBL_AUD_TURNO`; este corte no escribe el historial |
| Tiempo de Generar sobre el default | **diferido(perf-tiempo)** | p95 2235,8 ms > 1,5 s, medido en vacío |
| Volumen de Generar | **diferido(perf-volumen)** | un equipo, un horario |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Techo de las tres semillas | test | `indice-legacy.sh` 2026-09-23: 4/8 xhtml por semilla; unión 6 xhtml y 5 beans | 2026-09-23 | verificado |
| 2 | Generar manda `codItemEquipo` y el grupo | e2e | `npx playwright test e2e/grilla-turnos-equipo.spec.ts` · generar · POST `codItemEquipo=EQDEMO1001` `idGrpPrestTurEquipo=2` · 3 passed | 2026-09-24 | verificado |
| 3 | Eliminar consulta candidatos por ese código | e2e | mismo comando · eliminar · GET `codItemEquipo=EQDEMO1001` `idGrpPrestTurEquipo=2` | 2026-09-24 | verificado |
| 4 | Consulta muestra el nombre y la impresión lleva el equipo | e2e | mismo comando · grilla `EQUIPO DEMO` · query `equipo=EQUIPO DEMO` `codItemEquipo=EQDEMO1001` | 2026-09-24 | verificado |
| 5 | Generar escribe `ts.turno` tipo EQUIPO | escritura | `id=17255316` vigente · `SELECT id_turno FROM ts.turno WHERE TRIM(cod_item_equipo)='EQDEMO1001' AND fecha_hora_tur_ini::date=DATE '2026-11-29'` · LIBRE 2026-11-29 08:00 · el `id=17255315` se eliminó en la prueba de concurrencia (`idEliminacionAgenda=4`) | 2026-09-24 | verificado |
| 6 | Smoke de las tres pantallas | G6 | ealbo, 2026-09-24: «ok se ve bien eliminar generar y consultar agenda por equipo» | 2026-09-24 | verificado |
| 7 | Acceso: actor sin el menú TURNOS | acceso | perfil `ATENCION_TURNOS`; rol funcional ninguno. `npx tsx --tsconfig tsconfig.app.json e2e/acceso-grilla-equipo.check.ts` → `ok acceso grilla-equipo` exit 0. `RECEPCION` + `showAll=false` oculta TURNOS y las tres hojas. Con `ATENCION_TURNOS` están generar, eliminar y consulta | 2026-09-24 | verificado |
| 8 | p95, volumen y dos Generar solapados | no funcional | n=20 POST generar 2026-11-22 (ya cubierto, 0 inserts) · p95=2235,8 ms · min 333,8 · max 2444,5 · default escritura ≤ 1,5 s no se cumple → **diferido(perf-tiempo)**. Volumen **diferido(perf-volumen)**. Dos POST paralelos 2026-11-29 tras eliminar `id=17255315`: A **200** `turnosInsertados=0` «tiene turnos generados para el grupo Equipo Demo»; B **200** `turnosInsertados=1`. Quedó un solo `id=17255316` | 2026-09-24 | verificado |
| 9 | PDF BIRT muestra el nombre del equipo | PDF | `docs/cortes/turnos/turnos-grilla-equipo/evidencia/consulta-agendas-equipo-2026-11-29.pdf` · 2 KB · 1 pág · encabezado y columna `EQUIPO DEMO` · `POST http://localhost:8082/api/v1/reports/run` 200 | 2026-09-24 | verificado |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | e2e-migrado |
| Viaje generar | Radio Equipo → lupa `EQUIPO DEMO` → grupo 2 → Generar. Spec `Hospital-Web/e2e/grilla-turnos-equipo.spec.ts` |
| Viaje eliminar | Mismo north → Consultar → tildar LIBRE → confirmar |
| Viaje consulta | Mismo north → mes S → día 14 → columna `EQUIPO DEMO` → Imprimir (query, mock) |
| Fixture | mocks nombrados `EQDEMO1001` / grupo 2. La fila real del ledger 5 es PG, no el mock |
| Legacy e2e | no |
| Comando | `npx playwright test e2e/grilla-turnos-equipo.spec.ts` · 3 passed · 2026-09-24 |
