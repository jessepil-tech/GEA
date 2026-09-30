---
title: SDD — T5.5 · consulta agenda
description: >-
  Ítem Turnero Consulta Agenda en /turnos/agenda: filtros + grilla
  ts.turno por rango de fechas; infoTurno T5. No es T4 consulta agendas.
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas
---

# T5.5 — Consulta Agenda (`turnos-agenda-consultas`)

**Estado:** **gate-done** 2026-09-15 (G6 smoke ops **PASS** — Francisco: «ok lo veo bien a consultar agenda»). Clarify **FIRME Camino 1** 2026-09-14. D-TUR-59 · D-TUR-60.  
Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5).  
Hijos (cola corta, no WAIVE): [`turnos-agenda-consultas-pdf/`](../turnos-agenda-consultas-pdf/) **gate-done** · [`turnos-agenda-consultas-export/`](../turnos-agenda-consultas-export/) **gate-done**. Siguiente accordion: [`turnos-agenda-preagenda/`](../turnos-agenda-preagenda/).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A7.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md) — **gate-done** 2026-09-15.  
Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()` en `BBConsultaAgenda`.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (JDBC `ts.turno`; no porta `f_get_turnos_fecha`) | liberado |
| Rango Flyway | ninguno nuevo (hijo PDF **V56**) | liberado |
| Tablas `ts` que escribe | ninguna (consulta) | liberado |
| Rama | `dev/dev` | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Combos centro/servicio/personal | no | T5 | disponible |
| `ts.turno` rango | no | dump / T4–T5 | disponible |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrado) |
| Reservas | liberadas |
| Fixture | resuelto |
| Evidencia | [verify-report.md](verify-report.md) |
| Diferidos abiertos | Equipo cerrado en [`turnos-agenda-consulta-equipo`](../turnos-agenda-consulta-equipo/) |
| Próximo paso | ninguno en este corte |

Ruta: **`/turnos/agenda`** (NO fork). Layout HIS: accordion 170 + north filtros + tabla fill + south (Imprimir live cable / Excel live hijo). G6 visual PDF = hijo.

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap · **PASS** 2026-09-15 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy north / tabla / south / toast |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Turnero + north + grilla + info |
| [inventario-validaciones.md](inventario-validaciones.md) | `BBConsultaAgenda.actBtnConsultar` |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `f_get_turnos_fecha` → JDBC `ts.turno` |

**No es** T4 `consultaAgendasGeneradas.xhtml` (`/configuracion/grilla-turnos-consulta`).  
**No es** `consultaTurnos.xhtml` / `consultaTurnosAsignadosXOperador` (menú TURNOS).  
**No es** Pre-agenda (`turnos-agenda-preagenda`).  
**No es** Excel POI (`turnos-agenda-consultas-export`).  
**No es** re-migrar `ConsultaAgenda.rptdesign` (ya en Hospital-Reports).  
G6 visual PDF con filas: hijo [`turnos-agenda-consultas-pdf/`](../turnos-agenda-consultas-pdf/) **gate-done** 2026-09-15.  
El combo Equipo quedó en [`turnos-agenda-consulta-equipo`](../turnos-agenda-consulta-equipo/).  
**No es** HOS-APP.
