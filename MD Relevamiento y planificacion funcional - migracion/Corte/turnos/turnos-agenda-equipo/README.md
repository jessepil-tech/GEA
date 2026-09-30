---
title: SDD — Combo Equipo en Agenda (D-TUR-17, tramo 2)
description: Enciende el combo Equipo de la Agenda para ver y otorgar la grilla ya generada.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-equipo
---

# turnos-agenda-equipo

**Estado:** **gate-done** 2026-09-28. Clarify **FIRME** (ealbo). Rama `dev/tur-agenda-equipo`. G6, `17255287` y par sobre `17255288`. Volumen y auditoría diferidos.

La grilla en modo equipo está **gate-done**. Este tramo enciende el combo Equipo del north de `agenda.xhtml` para listar la oferta y otorgar sobre un turno `EQUIPO`.

Las otras hojas que dejaron el filtro apagado no entran: juntas superan el techo. Quedan en [`turnos-agenda-equipo-hermanas/`](../turnos-agenda-equipo-hermanas/).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** (T5 ya rediseñó otorgar) | package TURNOS **libre** |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | `turno` (otorgar sobre un hueco EQUIPO) | **liberado** 2026-09-28 |
| Rama | `dev/tur-agenda-equipo` | checkout local en Api, Web y Migration. Sin push |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 | no | T1/T2 | vigente |
| Equipo `EQDEMO1001` | no | hab + horarios | vigente |
| Turno de equipo otorgado | no | este corte, G6 | `id_turno=17255287` · 2026-09-28 08:00 · OTORGADO · paciente 20001 |
| Paciente para otorgar | no | padres de Agenda ya migrada | vigente |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-28 |
| Reservas | writer `ts.turno` **liberado** · Flyway n/a · BODY libre |
| Universo firmado | ealbo, 2026-09-28 |
| Fixture | padres vigentes, incluido el hueco `17255316` |
| Evidencia | [verify-report.md](verify-report.md) · ledger verificado · **PASS** con `diferido(auditoria)` y `diferido(perf-volumen)` |
| Diferidos abiertos | [`turnos-agenda-equipo-hermanas`](../turnos-agenda-equipo-hermanas/) · `diferido(auditoria)` · `diferido(perf-volumen)` |
| Próximo paso | ninguno en este slug |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify FIRME |
| [plan.md](plan.md) | Orden |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy del combo |
| [inventario-validaciones.md](inventario-validaciones.md) | Validación |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Cambio de equipo |
| [inventario-geometria.md](inventario-geometria.md) | Misma celda del north |

**No es** historial, suspender, cola, consulta de agenda ni sobreturno.
