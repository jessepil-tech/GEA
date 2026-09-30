---
title: SDD — Equipo en Suspender y Quitar suspensión
description: Enciende el radio Equipo de suspender y quitar suspensión. No redibuja las hojas.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-grilla-suspender-equipo
---

# turnos-grilla-suspender-equipo

**Estado:** **gate-done** 2026-09-28. Rama `dev/tur-historial-equipo`.

Las hojas ya están **gate-done** en [`turnos-grilla-suspender/`](../turnos-grilla-suspender/). Este corte enciende el radio Equipo que quedó disabled.

No entra la cola, la consulta, el sobreturno ni los múltiples. Siguen en [`turnos-agenda-equipo-hermanas/`](../turnos-agenda-equipo-hermanas/).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** | package TURNOS **libre**. El padre ya portó `f_suspender_turnos` / `f_quitar_suspension` |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | `turno`, `hist_turno`, `suspension_agenda_turnos` | el writer es el del padre, ya liberado |
| Rama | `dev/tur-historial-equipo` | checkout local en Api, Web y Migration. Sin push |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · `EQDEMO1001` | no | hab + horarios | vigente |
| Turno de equipo | no | grilla de equipo | `id_turno=17255287` · 28/09 08:00 · quedó `LIBRE` después de quitar |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-28 |
| Reservas | writer del padre · Flyway n/a · BODY libre |
| Universo firmado | radio Equipo de `suspenderGrillaTurnos.xhtml` y `quitarCancelacionGrillaTurnos.xhtml` |
| Fixture | turno `17255287` vigente |
| Evidencia | [verify-report.md](verify-report.md) · ledger 6/6 verificado · **PASS** con `diferido(perf-volumen)` |
| Diferidos abiertos | hermanas restantes · `diferido(perf-volumen)` · `diferido(auditoria)` del padre |
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
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Radio Equipo |
