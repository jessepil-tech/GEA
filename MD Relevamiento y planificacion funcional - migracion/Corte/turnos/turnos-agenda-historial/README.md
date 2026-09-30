---
title: SDD — T6.3 · Historial Turnos (Turnero)
description: >-
  Ítem Turnero Historial Turnos en /turnos/agenda: lista ts.hist_turno
  + popup Información Turno. Excel south → hijo.
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial
---

# T6.3 — Historial Turnos (`turnos-agenda-historial`)

**Estado:** **gate-done** 2026-09-18 · Clarify **FIRME Camino 1** D-TUR-70.  
Padre T6 `turnos-ciclo-vida` (resto **diferido**: reemplazo / vencidos).  
Hermanos: T6.2 [`turnos-agenda-cola-reasignar/`](../turnos-agenda-cola-reasignar/) · T6.4 [`turnos-grilla-suspender/`](../turnos-grilla-suspender/) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A8.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Proceso: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).  
Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()` en `BBHistorialTurno`.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (JDBC `ts.hist_turno`; Hibernate Criteria, sin `f_/p_`) | n/a · no toma lock TURNOS |
| Rango Flyway | **n/a** (tabla en dump / V43) | — |
| Tablas `ts` que escribe | ninguna (solo SELECT) | n/a |
| Rama | `dev/t63-historial-turnos` (Api/Web/Migration) | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Combos centro/servicio/profesional | no | T5 buscadores | disponible |
| Filas `ts.hist_turno` | no | T5 otorga/libera (y T4 hist al eliminar) | dump 2026-09-18: COUNT=17 · hoy=4 · G6 ve filas |
| Paciente / prep / req / docs del popup | no | T5.1 / T5.1e-q | disponible |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** publicado · **gate-done** |
| Reservas | JDBC n/a · Flyway n/a · rama local (liberar BODY n/a) |
| Fixture | padres T5; hist = acto otorga/libera, no seed · COUNT 17 / hoy 4 |
| Evidencia | [verify-report.md](verify-report.md) · G6 «si se ve ok» (info) · lista · Excel hijo |
| Diferidos abiertos | D-TUR-17 · T6 padre resto |
| Próximo paso | ninguno de este corte |

Ruta: **`/turnos/agenda`** (NO fork). Accordion 170 ya T5.2. **Sin** calendario 260.

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate anti-gap |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy north / tabla / popup / south |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Turnero + Consultar + info |
| [inventario-validaciones.md](inventario-validaciones.md) | `WRONG_INTERVAL_HOUR` |
| [inventario-geometria.md](inventario-geometria.md) | North 95/70 · col info 24 · popup 1200×512 |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `ts.hist_turno` G1 n/a |

**No es** `consultaHistorialTurno.xhtml` (menú TURNOS) — [`turnos-consultas-operador/`](../turnos-consultas-operador/).  
**No es** HOS-APP `historialTurno.xhtml`.  
**No es** Excel south (hijo). **No es** equipo usable (D-TUR-17).  
**No es** suspender / reemplazo / vencidos.
