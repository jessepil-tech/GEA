---
title: SDD — Equipo en Consulta de Agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-consulta-equipo
---

# turnos-agenda-consulta-equipo

**Estado:** **gate-done** 2026-09-28. Rama `dev/tur-historial-equipo`.

La hoja ya está **gate-done** en [`turnos-agenda-consultas/`](../turnos-agenda-consultas/). Este corte enciende el combo Equipo de la consulta.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** | package TURNOS **libre** |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | ninguna | — |
| Rama | `dev/tur-historial-equipo` | checkout local. Sin push |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · `EQDEMO1001` | no | hab + horarios | vigente |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-28 |
| Universo firmado | el combo Equipo de la consulta |
| Evidencia | [verify-report.md](verify-report.md) · **PASS** con `diferido(perf-volumen)` |
| Diferidos abiertos | `diferido(perf-volumen)` |
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
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Combo |
