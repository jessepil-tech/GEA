---
title: Verify — T5.6 pre-agenda turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-preagenda.verify
---

# Verify — Pre-agenda

**Gate:** **PASS / gate-done Camino 1** 2026-09-15. D-TUR-61. G6 smoke ops **PASS** (Francisco: «ok pre agenda se ve bien»).  
**No** cierra T5 padre ni T5.5 hijos ni menú `consultaPreagenda` ni alta ATENCION.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Turnero Pre Agenda Turnos | **done** | xhtml L89–91 |
| North + Consultar | **done** | `preAgendaTurnos.xhtml` |
| Tabla PENDIENTE `ts.pre_agenda_turno` | **done** | JDBC `listarPreAgenda` |
| Acciones → ASIGNAR TURNO | **done** | overlay + `asignarDesdePreagenda` |
| UPDATE al otorgar Agenda | **done** | `idPreAgendaTurno` en otorgar |
| Alta (prescripción) | **N/A este xhtml** | fuera (ATENCION) |
| Menú consulta pre-agenda | **diferido** | `turnos-consultas-preagenda` |
| UPDATE det_prescrip al otorgar | **diferido** | ambulatorio |
| Equipo usable | **diferido** | D-TUR-17 |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Turnero abre vista | sí | **e2e-migrado** | `turnos-agenda.spec.ts` |
| Toast intervalo fechas | sí | **e2e-migrado** | mismo |
| Consultar filas PENDIENTE | sí | **e2e-migrado** | fixture |
| Asignar → Agenda prefijada | sí | **e2e-migrado** | mismo |
| Legacy HIS | — | **no** | — |

E2E mocks CI ≠ G6. Universo de CU = esta matriz.

## Paridad UI (xhtml)

Inventarios G0: **docs 2026-09-15**. Gate UI **antes** de template (después FIRME).

| Control | Estado | Nota |
|---------|--------|------|
| geometría | **done** | accordion 170 + north filtros + tabla fill; sin calendario 260; sin south |
| Inventario copy | **done** (docs) | north / tabla / menú |
| Inventario validaciones | **done** (docs) | `WRONG_INTERVAL_DATE_3` |
| Inventario interacción | **done** (docs) | Turnero + Consultar + Asignar |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Turnero abre vista Pre-agenda | e2e | `turnos-agenda.spec.ts` | CI | **verificado** |
| 2 | Consultar lista PENDIENTE | e2e | G6 Francisco `/turnos/agenda` | 2026-09-15 «ok pre agenda se ve bien» | **verificado** |
| 3 | Seed DEV `ts.pre_agenda_turno` id 70001 | test | Flyway V55 | 2026-09-15 | **verificado** |
| 4 | Acceso: perfil de menú Pre Agenda Turnos | e2e | actor G6 con perfil; «sin el rol» no reabrir | T5.6 G6 | **verificado** |
| 5 | Volumen: filas de la tabla `ts.pre_agenda_turno` (DEV seed / piloto vacío) | test | dump 0; V55 `70001` | 2026-09-15 | **verificado** |

## Smoke

**G6 stack real — PASS** 2026-09-15 (Francisco, `/turnos/agenda` vista pre-agenda: lista, convenio/plan, Asignar).
Dump piloto: `ts.pre_agenda_turno` vacío (alta = ATENCION, fuera Camino 1). Seed DEV **V55** id `70001`. Convenio/plan grilla = última atención paciente (dump sin `det_prescrip_*`).

## Resultado

**PASS / gate-done Camino 1.** Diferidos: menú `consultaPreagenda` · alta ATENCION · `prestPreAgendaServ` · UPDATE det_prescrip · equipo D-TUR-17 · T6 padre.
