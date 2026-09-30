---
title: SDD — Grilla en modo equipo (D-TUR-17, tramo 1)
description: >-
  Radio Equipo en generar, eliminar y consulta de agendas.
  Sin filtro Equipo de la Agenda.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo
---

# Grilla en modo equipo (`turnos-grilla-equipo`)

**Estado:** Clarify **FIRME** (ealbo, 2026-09-24). Rama `dev/tur-grilla-equipo`.  
Cobra el tramo de **generar / eliminar / consultar** que D-TUR-17 dejó fuera de T4, después de la hab ([`turnos-hab-equipo/`](../turnos-hab-equipo/)) y los horarios ([`turnos-horarios-equipo/`](../turnos-horarios-equipo/)).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Instalación de referencia: Call Center Demo.

Índice (2026-09-23), una semilla por hoja:

| Semilla | xhtml | beans | firmas | rptdesign |
|---------|------:|------:|-------:|----------:|
| `generacionGrillaTurnos` | 4 / 8 | 3 / 15 | 0 | 0 |
| `eliminarGrillaTurnos` | 4 / 8 | 3 / 15 | 0 | 0 |
| `consultaAgendasGeneradas` | 4 / 8 | 3 / 15 | 0 | 1 (no entra: hijo de impresión ya cerrado) |

Unión de este corte: 3 hojas + 2 buscadores + `contractDefault` = 6 xhtml. Beans de dominio: los tres `BB*` + dos buscadores = 5. Techo ok.  
El reporte de la consulta no se reabre.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** (T4 ya rediseñó el cálculo; el índice ve 0 firmas en el bean) | package TURNOS **libre** |
| Rango Flyway | **n/a** (`ts.turno` ya está en el dump) | — |
| Tablas `ts` que escribe | `turno` y, al eliminar un turno con paciente, `turno_a_reasignar` | **liberado** 2026-09-24 |
| Rama | `dev/tur-grilla-equipo` (Api + Web + Migration) | checkout local |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 | no | T1/T2 | vigente |
| `equipo_serv_centro` `EQDEMO1001` | no | seed de la hab | vigente |
| Hab equipo vigente | no | acto de [`turnos-hab-equipo`](../turnos-hab-equipo/) | vigente |
| Grupo, prestación y horario del equipo | no | acto de [`turnos-horarios-equipo`](../turnos-horarios-equipo/) | vigente (grupo id=2, horario id=1) |
| Filas `ts.turno` del equipo | sí (las deja Generar) | acto del CU | no se seedean |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-24 |
| Reservas | writer `ts.turno` **liberado** · Flyway n/a · BODY libre |
| Universo firmado | radio Equipo en generar, eliminar y consultar |
| Fixture | padres vigentes |
| Evidencia | [verify-report.md](verify-report.md) · ledger verificado · **PASS** con `diferido(perf-tiempo)` y `diferido(perf-volumen)` |
| Diferidos abiertos | horario especial · `diferido(auditoria)` · `diferido(perf-tiempo)` · `diferido(perf-volumen)`. Filtro Equipo de Agenda [`turnos-agenda-equipo`](../turnos-agenda-equipo/) **gate-done** 2026-09-28 |
| Próximo paso | ninguno en este slug |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify propuesto |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy del radio Equipo |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones del modo equipo |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Interacción |
| [inventario-geometria.md](inventario-geometria.md) | Geometría |

**No es** el combo Equipo de la Agenda. **No es** otorgar un turno de equipo.
