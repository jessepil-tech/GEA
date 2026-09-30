---
title: SDD — Equipo en Historial de Agenda
description: Enciende el combo Equipo del historial de Agenda. No redibuja la hoja.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-historial-equipo
---

# turnos-agenda-historial-equipo

**Estado:** en curso (ealbo, 2026-09-28, «comenzar con historial primero»). Rama `dev/tur-historial-equipo`.

La hoja Historial ya está **gate-done** en [`turnos-agenda-historial/`](../turnos-agenda-historial/). Este corte enciende el combo Equipo que quedó `disabled`.

No entra el menú `consultaHistorialTurno`. Las otras hojas del filtro siguen en [`turnos-agenda-equipo-hermanas/`](../turnos-agenda-equipo-hermanas/).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** | package TURNOS **libre** |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | ninguna | — |
| Rama | `dev/tur-historial-equipo` | checkout local en Api, Web y Migration. Sin push |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · `EQDEMO1001` | no | hab + horarios | vigente |
| Hist de equipo | no | el otorgar de Agenda | `id_hist_turno=9676753` · turno `17255287` · `EQDEMO1001` |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-28 |
| Reservas | sin writer · Flyway n/a · BODY libre |
| Universo firmado | combo Equipo de `historialTurno.xhtml` |
| Fixture | hist `9676753` vigente |
| Evidencia | [verify-report.md](verify-report.md) · ledger 6/6 verificado · **PASS** con `diferido(perf-volumen)` |
| Diferidos abiertos | hermanas restantes · `diferido(perf-volumen)` |
| Próximo paso | ninguno en este slug |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance |
| [plan.md](plan.md) | Orden |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-geometria.md](inventario-geometria.md) | Misma celda |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy |
| [inventario-validaciones.md](inventario-validaciones.md) | Validación |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Cambio de equipo |
