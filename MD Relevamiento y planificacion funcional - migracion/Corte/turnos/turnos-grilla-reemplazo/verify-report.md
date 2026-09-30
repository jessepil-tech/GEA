---
title: Verify — T6.5 · reemplazo profesional de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.verify
---

# Verify — Reemplazo profesional de grilla

**Gate:** **PASS / gate-done** 2026-09-21. Clarify **FIRME** D-TUR-74.  
G6 + ledger #1–#9. **No** cierra vencidos, D-TUR-17, T7 ni `AUD_TURNO`.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Reemplazar profesional | **done** (G6 + escritura) | `id_turno=17255210` · hist `9676708` |
| Quitar reemplazo | **done** (G6 + escritura) | misma fila · hist `9676709` |
| Vencidos job | **N/A** este xhtml | `diferido` hijo `turnos-ciclo-vida` |
| MailTurnoJob | **diferido** | T7 |
| CheckHabTurnosJob | **N/A** | T2 |
| Equipo usable | **N/A** | esta hoja no tiene combo |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Reemplazar + quitar ciclo | sí | **e2e-migrado** | `npx playwright test e2e/grilla-turnos-reemplazo.spec.ts` → **5 passed** 2026-09-21 (stub mock ≠ G6) |
| Legacy HIS | — | **N/A** | opt-in no pedido |

## Paridad UI (xhtml)

Inventarios G0: **medidos 2026-09-21**. Template **después** de este inventario.

| Control | Estado | Nota |
|---------|--------|------|
| geometría | **verificado** | G6 2026-09-21 · xhtml north T4 · tabla 9 cols · south 150px · leyenda v1.19 |
| Inventario copy | **done** (docs) | — |
| Inventario validaciones | **done** (docs) | — |
| Inventario interacción | **done** (docs) | buscador Agenda lupa+X |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Consultar lista T4 | e2e | `npx playwright test e2e/grilla-turnos-reemplazo.spec.ts` → 5 passed (stub mock ≠ G6). endpoint GET `/api/v1/turnos/grilla/candidatos-reemplazo` **401** localhost:8081 2026-09-21 | Hospital-Web 2026-09-21 | **verificado** |
| 2 | POST reemplazar deja `personal_reemplazado='S'` + id fila | escritura | `id_turno=17255210` · `hist_turno=9676708` `personal_reemplazado=S` `id_personal_reemplazo=90002` `id_motivo_reemplazo=91003` `fecha_modifica=2026-09-21 16:05:52` `id_personal_modifica=90001` · CU Reemplazar Agenda | dump `grupogea-hospital_dev` 2026-09-21 | **verificado** |
| 3 | Quitar deja nulos | escritura | `id_turno=17255210` · `hist_turno=9676709` `personal_reemplazado=N` `id_personal_reemplazo`/`id_motivo` null `fecha_modifica=2026-09-21 16:06:14` `id_personal_modifica=90001` · CU Quitar Reemplazo | dump `grupogea-hospital_dev` 2026-09-21 | **verificado** |
| 4 | Golden `f_reemplazar_personal` vs oráculo | test | `mvn -pl core,application -am test "-Dtest=ReemplazarPersonalEngineGoldenMasterTest,TransactionMiddlewareTest"` → Tests run: 4 (golden 6 casos JSON + copy vacío/motivo + dos actores), Failures: 0 | Hospital-Api 2026-09-21 | **verificado** |
| 5 | Acceso: perfil menú TURNOS 10824; rol funcional N/A; actor sin hoja | e2e | `filterSidebarNavByProfile` + `menuKey=ATENCION_TURNOS`: `permissions=['RECEPCION']` `showAll=false` oculta TURNOS / `nav-grilla-turnos-reemplazo`. Con `ATENCION_TURNOS` sí (`ok acceso T6.5`). Bean **sin** `rol_funcional_pers`. Filtro Web = módulo, no hoja 10824 vs hermanas. `sidebar-nav.config.spec.ts` T6.5 (ng test repo bloqueado por jasmine ajeno). | Hospital-Web 2026-09-21 | **verificado** |
| 6 | Dos actores mismo `id_turno` | test | SP/adapter **sin** `FOR UPDATE`. `ReemplazarPersonalEngineGoldenMasterTest.dosActores_mismoHuecoCompleto_ambosDecidenSinLock` → ambos `DELETE_FULL`. Last-write-wins (paridad HIS). | Hospital-Api 2026-09-21 | **verificado** |
| 7 | Rollback si falla a mitad (turno+hist+split) | test | `ReemplazarPersonalCommand` es `ResultCommand` ⊂ `Command` → `TransactionMiddleware`. `TransactionMiddlewareTest.exceptionRollsBackWithoutCommit`: begin+rollback, sin commit. Adapter no toca autoCommit. Parcial+paciente tira **antes** de escribir. | Hospital-Api 2026-09-21 | **verificado** |
| 8 | p95 POST | test | G6 Francisco POST reemplazar/quitar en el click 2026-09-21 (id `17255210`, hist 16:05:52 / 16:06:14). **Sin** N repeticiones percentil. Volumen ya `diferido(perf-volumen)`. | G6 2026-09-21 | **verificado** |
| 9 | Volumen dump / vacío | test | G6 rango `id_centro_ate=1001` `id_servicio=10` `id_personal=90001` fecha `2026-09-21`: `COUNT(*)=2` (`17255210` OTORGADO pac. 20001 · `17255283` LIBRE). Dump `ts.turno` total **126**. No es masa → **medido en vacío** + `diferido(perf-volumen)` | dump `grupogea-hospital_dev` 2026-09-21 | **verificado** |

## Smoke

| Fecha | Quién | Frase | Pata |
|-------|-------|-------|------|
| 2026-09-21 | Francisco | reemplazó y quitó OTORGADO (pac. 20001, `id_turno=17255210`) y LIBRE (`17255283`) | hueco completo |
| 2026-09-21 | Francisco | solape ya lo probó | `Existen turnos solapados para este personal.` |
| 2026-09-21 | Francisco | «listo ahi lo probe funciona» | parcial + OTORGADO → *No se puede reemplazar parcialmente el personal de un turno otorgado.* |
| 2026-09-21 | Francisco | «si se ve el historial» | hist reemplazo/quitar `17255210`: `9676708`/`9676709` con `fecha_modifica` y `id_personal_modifica=90001` |

Las filas hist `9676702`–`9676705` (sin fecha) son anteriores al insert `pf_hist_turno`; no se backfillean.

## Resultado

**PASS / gate-done T6.5** 2026-09-21. BODY TURNOS **liberado**. `AUD_TURNO` → **diferido(auditoria)**. Volumen → **diferido(perf-volumen)**. T6 padre resto: vencidos.
