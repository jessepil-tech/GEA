---
title: Verify — T4 Turnos generación de grilla
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.turnos-generacion-grilla
---

# Verify — T4

**Gate:** **PASS / gate-done** 2026-09-04 · Clarify **FIRME** 2026-09-01 · G1–G6.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Generar grilla serv/pers | **done G5** (API+port+golden+UI) | POST `/api/v1/turnos/grilla/generar` |
| Eliminar grilla serv/pers | **done G5** (API+port+UI; SMS T7 diferido) | POST `/api/v1/turnos/grilla/eliminar` |
| Candidatos a eliminar (lista previa UI) | **done G5** (mínima, agregada junto con UI) | GET `/api/v1/turnos/grilla/candidatos-eliminar` |
| Consulta agendas generadas | **done G5** (API+query+UI+drill-down) | GET `/api/v1/turnos/grilla/consulta-agendas` |
| Imprimir consulta agendas (BIRT) | **done G6** | GET `.../imprimir.pdf` · smoke stack real 2026-09-04 · hijo [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/) |
| Generar/equipo | diferido | D-TUR-17 |
| Horario especial en generación | diferido | D-TUR-15 / D-TUR-20 |
| Suspender / quitar / reemplazo grilla | diferido T6 | D-TUR-18 |
| SMS/mail al eliminar | diferido T7 | D-TUR-19 |
| Generar desde T3 horario (UI) | diferido UI | spec C7 |
| Reservar / otorgar | diferido T5 | — |

## Viaje Playwright

Varios viajes en el mismo corte: **una fila por viaje**.

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Generar grilla | sí | **e2e-migrado** | `Hospital-Web/e2e/grilla-turnos-generar-eliminar.spec.ts` |
| Eliminar grilla | sí | **e2e-migrado** | Mismo spec (viaje eliminar). |
| Consulta + drill-down + imprimir | sí | **e2e-migrado** | `Hospital-Web/e2e/grilla-turnos-consulta.spec.ts` · [`../turnos-consulta-agendas-imprimir/migrated-flow.md`](../turnos-consulta-agendas-imprimir/migrated-flow.md) |
| Legacy HIS | — | **no** | Sin fixture Oracle vigente (`origin`); no scan |

Fixture consulta (G6): `HOSPITAL-DEMO` / `CLINICA MEDICA` / `2026-09-14` — **smoke ops PASS** 2026-09-04.  
E2E consulta (mocks CI) complementa; no sustituye G6.

## Paridad UI (xhtml)

Fuente: `generacionGrillaTurnos.xhtml` · `eliminarGrillaTurnos.xhtml` · `consultaAgendasGeneradas.xhtml`.  
Inventarios G0: [`inventario-copy-msg.md`](inventario-copy-msg.md) · [`inventario-validaciones.md`](inventario-validaciones.md).

| Control | Estado | Nota |
|---------|--------|------|
| Inventario msg.* ↔ labels.ts | **done** | `grilla-turnos-labels.ts` (G5) |
| Inventario validaciones BB/MessageBundle/SP | **done** (UI) | `grilla-turnos-validation.ts` — subset cliente (fechas/horas/día/turno); resto ya cubierto server-side |
| **Disposición generar** vs xhtml | **done** | cascada centro/servicio (no buscador HAB en serv); buscador solo profesional; obs table + Generar/Volver |
| **Disposición eliminar** vs xhtml | **done** | north 200\|1fr\|200\|1fr; cascada serv / buscador pers; grupo + esp. rango; fechas/horas; días + Consultar; south Eliminar\|Volver |
| **Disposición consulta** vs xhtml | **done** (matriz + drill-down + imprimir) | G6 smoke 2026-09-04 |
| Buscadores personal (display≠criterio) | **done** | `HabBuscadorDialogComponent` solo en opción profesional (generar/eliminar/consulta); equipo D-TUR-17 |
| Tabla observaciones post-generar | **done** | contrato legacy — tabla siempre visible + emptyMessage + toast |
| Confirm eliminar (modal, no window.confirm) | **done** | `ConfirmDialogComponent` + `confirmEliminarAgenda` (texto inventario) |
| Dark mode | **done** | clases `dark:` reusadas de `HORARIOS_TURNOS_UI`/`GRILLA_TURNOS_UI` |
| Imprimir (consulta) | **done** (gaps PG documentados) | Botón habilitado con día+turnos; Api→Reports `ConsultaAgendaGeneradas`; ver hijo imprimir |
| Gaps DDL | **done** V43/V44 | [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md) |

## Smoke

**Gate v1.8 disposición:** cerrada (generar + eliminar + consulta).

**G6 stack real — PASS 2026-09-04** (smoke manual ops):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Identity `:8080` · Api `:8081` · Web `:4200` · Reports `:8082` | **PASS** |
| 2 | Consulta agendas → mes S → día con turnos → **Imprimir** | **PASS** |
| 3 | PDF BIRT con filas (piloto `HOSPITAL-DEMO` / `CLINICA MEDICA` / `2026-09-14`) | **PASS** |

## Resultado

**PASS / gate-done T4** con diferidos explícitos (D-TUR-15/17/18/19, T5–T7, `estadoTurno` imprimir pendiente relevar en hijo).  
T4 cerrado en gobierno (TSK-ops-g6-2). Siguiente corte Turnos: **T5** `turnos-agenda-otorgar`.
