---
title: SDD — T5.6 · Pre-agenda turnos (Turnero)
description: >-
  Ítem Turnero Pre Agenda Turnos en /turnos/agenda: lista PENDIENTE
  ts.pre_agenda_turno + Asignar → Agenda. No es consultaPreagenda del menú TURNOS.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-preagenda
---

# T5.6 — Pre-agenda (`turnos-agenda-preagenda`)

**Estado:** **gate-done Camino 1** 2026-09-15 (G6 smoke ops **PASS**).  
Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5). Previo accordion: [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A7.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md) — **gate-done** 2026-09-15.  
Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()` en `BBPreAgendaTurnos`.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (JDBC `ts.pre_agenda_turno`; alta ATENCION fuera) | liberado |
| Rango Flyway | **V54–V55** | liberado (cerrado) |
| Tablas `ts` que escribe | `pre_agenda_turno` (DDL V54; seed DEV V55; UPDATE al otorgar) | liberado |
| Rama | `dev/dev` | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Paciente / servicio / prestación | no | T5 | disponible |
| `ts.pre_agenda_turno` PENDIENTE | seed DEV sí (`70001`); dump piloto vacío | V55 / ATENCION | disponible (DEV) / vacío (piloto) |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrado) |
| Reservas | liberadas |
| Fixture | resuelto (Camino 1: lista; alta ATENCION fuera) |
| Evidencia | [verify-report.md](verify-report.md) |
| Diferidos abiertos | [`turnos-consultas-preagenda/`](../turnos-consultas-preagenda/) · alta ATENCION · D-TUR-17 |
| Próximo paso | ninguno en este corte |

Ruta: **`/turnos/agenda`** (NO fork). Layout HIS: accordion 170 + north filtros + tabla fill. **Sin** calendario 260. **Sin** south Imprimir/Excel.

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap · **PASS Camino 1** |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy north / tabla / menú |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Turnero + Consultar + Asignar |
| [inventario-validaciones.md](inventario-validaciones.md) | `WRONG_INTERVAL_DATE_3` |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `ts.pre_agenda_turno` G1 |

**No es** `consultaPreagenda.xhtml` (menú TURNOS) — [`turnos-consultas-preagenda/`](../turnos-consultas-preagenda/).  
**No es** alta de filas desde prescripción (package ATENCION).  
**No es** `prestPreAgendaServ.xhtml` (config servicio).  
**No es** HOS-APP `preAgendaTurnos.xhtml`.  
**No es** Consulta Agenda T5.5 ni T4.  
**No es** equipo usable (D-TUR-17).
