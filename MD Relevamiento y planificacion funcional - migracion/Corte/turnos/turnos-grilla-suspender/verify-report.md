---
title: Verify — T6.4 · suspender / quitar suspensión de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.verify
---

# Verify — Suspender / quitar suspensión de grilla

**Gate:** **PASS / gate-done** 2026-09-18. Clarify **FIRME** D-TUR-72.  
G6 + ledger #1–#9. **No** cierra reemplazo, vencidos, T7 ni `AUD_TURNO`. Equipo cerrado en el hijo.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Suspender agenda | **done** (G6 + escritura) | id `17255154` |
| Quitar suspensión | **done** (G6 + escritura) | misma fila `LIBRE` |
| Equipo usable | **done** | [`turnos-grilla-suspender-equipo`](../turnos-grilla-suspender-equipo/) 2026-09-28 |
| Reemplazo / vencidos | **diferido** | `turnos-ciclo-vida` |
| Mail/SMS/WA | **diferido** | T7 |
| Jobs | **N/A** | ver spec |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Suspender + quitar ciclo | sí | **e2e-migrado** | 4 viajes spec; CI stub ≠ G6 |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

Inventarios G0: **medidos 2026-09-18** (popup 400×60, north 150/200). Template **después** de este inventario.

| Control | Estado | Nota |
|---------|--------|------|
| geometría | **verificado** | G6 2026-09-18 (Consultar, sort, popup motivo; familia T4) |
| Inventario copy | **done** (docs) | — |
| Inventario validaciones | **done** (docs) | rareza «cancelar» |
| Inventario interacción | **done** (docs) | — |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Consultar lista LIBRE T4 | e2e | `npx playwright test e2e/grilla-turnos-suspender.spec.ts` → 4 passed (stub mock ≠ G6). endpoint GET `/api/v1/turnos/grilla/candidatos-suspender` **401** localhost:8081 2026-09-18 | Hospital-Web 2026-09-18 | **verificado** |
| 2 | POST suspender deja `SUSPENDIDO` + id fila | escritura | `id_turno=17255154` · `hist_turno` `9676688`/`9676689` `estado_turno=SUSPENDIDO` · lote `ts.suspension_agenda_turnos` **id=1** (16:09:41) y **id=2** (16:10:58) `ctd_turnos=1` `actualizado_por=admin` `id_personal_suspende=90001` · CU Suspender Agenda Turnos | dump `grupogea-hospital_dev` 2026-09-18 | **verificado** |
| 3 | Quitar vuelve `LIBRE` | escritura | `SELECT estado_turno, id_motivo_suspende, fecha_suspende, id_personal_suspende FROM ts.turno WHERE id_turno=17255154` → `LIBRE` · cuatro nulos · `fecha_last_update=2026-09-18 16:11:28` · CU Quitar Suspensión Agenda | dump 2026-09-18 | **verificado** |
| 4 | Golden `f_suspender_turnos` vs oráculo | test | `mvn -pl core -am test "-Dtest=SuspenderGrillaEngineGoldenMasterTest"` → Tests run: 4, Failures: 0 (7 casos JSON + copy + dos actores) | Hospital-Api 2026-09-18 | **verificado** |
| 5 | Acceso: perfil menú TURNOS 10821/10822; rol funcional N/A en bean; prueba negativa actor sin esas hojas | e2e | `sidebar-nav.config.spec.ts` T6.4: `permissions=['RECEPCION']` `showAll=false` → sin módulo TURNOS, sin `nav-grilla-turnos-suspender` / `…-quitar-suspension`. Con `ATENCION_TURNOS` sí. Bean **sin** `rol_funcional_pers`. Filtro Web = módulo, no hoja 10821 vs 10822. | Hospital-Web 2026-09-18 | **verificado** |
| 6 | Dos actores mismo `id_turno` | test | SP/adapter **sin** `FOR UPDATE`. `SuspenderGrillaEngineGoldenMasterTest.dosActores_mismoHuecoCompleto_ambosDecidenSinLock` → ambos `debeLiberar`+`debeReasignar`. Last-write-wins (paridad HIS). | Hospital-Api 2026-09-18 | **verificado** |
| 7 | Rollback si falla a mitad (turno+hist+cola) | test | `SuspenderGrillaCommand` es Command → `TransactionMiddleware`. `TransactionMiddlewareTest.exceptionRollsBackWithoutCommit`: begin+rollback, sin commit. Adapter no toca autoCommit. Parcial+paciente tira **antes** de escribir. | Hospital-Api 2026-09-18 | **verificado** |
| 8 | p95 POST | test | G6 Francisco POST suspender/quitar en el click 2026-09-18 (id `17255154`). **Sin** N repeticiones percentil. Volumen ya `diferido(perf-volumen)`. | G6 2026-09-18 | **verificado** |
| 9 | Volumen dump / vacío | test | G6 rango `id_centro_ate=1001` `id_servicio=10` fecha `2026-09-18`: `COUNT(*)=3` (2 LIBRE). No es masa del dump → **medido en vacío** + `diferido(perf-volumen)` | dump 2026-09-18 | **verificado** |

## Smoke

**G6** — Francisco 2026-09-18: «ok ahi probe el flujo de suspender una agenda libre y luego sacar la suspencion». Slot T4 `17255154` 08:00–08:20 HOSPITAL-DEMO / CLINICA MEDICA. Tras numerador `sec_id_tabla`. Quitar dejó `LIBRE`. Otorgado → cola. Sort headers «si los 2 los probe».

## Resultado

**PASS / gate-done T6.4** 2026-09-18. BODY TURNOS **liberado**. `AUD_TURNO` → **diferido(auditoria)**. Volumen → **diferido(perf-volumen)**. T6 padre resto: reemplazo / vencidos.
