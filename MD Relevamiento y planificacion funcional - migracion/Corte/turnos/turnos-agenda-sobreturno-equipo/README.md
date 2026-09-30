---
title: SDD — Equipo en el sobreturno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-agenda-sobreturno-equipo
---

# turnos-agenda-sobreturno-equipo

**Estado:** **gate-done** 2026-09-28. Rama `dev/tur-historial-equipo`.

La hoja ya está **gate-done** en [`turnos-agenda-sobreturno/`](../turnos-agenda-sobreturno/). Este corte enciende el combo Equipo del sobreturno.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** | package TURNOS **libre** |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | `turno` | el writer es el del padre |
| Rama | `dev/tur-historial-equipo` | checkout local. Sin push |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · `EQDEMO1001` | no | hab + horarios | vigente |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-28 |
| Universo firmado | el combo Equipo del sobreturno |
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
