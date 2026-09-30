---
title: SDD — T6.2 · cola Reasignación de Turnos
description: >-
  Accordion Reasignación de Turnos (turnosAReasignar.xhtml) en /turnos/agenda.
  Lista ts.turno_a_reasignar; gear IrAGrilla reusa T6.1; obs cola.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar
---

# turnos-agenda-cola-reasignar

Padre T6 (`turnos-ciclo-vida`) **sigue diferido** para suspender / reemplazo / vencidos / hist.  
Hermano: [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) T6.1 **gate-done** (engranaje de **fila** en Agenda — no este CU).  
Alimenta la cola: T4 eliminar grilla (V43 `ts.turno_a_reasignar`) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Clarify **FIRME Camino 1** 2026-09-16. D-TUR-64. Francisco: «ok».  
Instalación de referencia: Call Center Demo. Sin ramas `esClienteX()` en `BBTurnosAReasignar`.  
**No WAIVE:** HIS `turnosAReasignar.xhtml` + `BBTurnosAReasignar` live en accordion.  
**No** reabrir T6.1. **No** T6 padre entero. **No** Historial. Avisos HIS `disabled=true` → **WAIVE** (evidencia en spec).

Legacy: `turnosAReasignar.xhtml` · `BBTurnosAReasignar`. Semilla = basename xhtml (no hay ACCION de menú propia: vive en accordion de Agenda).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (JDBC equivalente; no porta TURNOS BODY) | n/a |
| Rango Flyway | **n/a** (tabla en dump / V43 ya aplicada; no `CREATE` ni seed) | — |
| Tablas `ts` que escribe | `ts.turno_a_reasignar` (UPDATE observaciones) | reservado este corte |
| Rama | `dev/t62-cola-reasignar` (local, rebased sobre `origin/dev/dev` 2026-09-17) | — |

No pisa el BODY TURNOS. Paralelo a P3 anunciador: sí (otro stream).

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Fila `ts.turno_a_reasignar` | no | T4 eliminar grilla con paciente (CU, no seed) | G6 2026-09-17: T4 insertó `302674`; otorgar DELETE → **COUNT=0**. **No seed.** |
| Combos centro/servicio/personal | no | T5 | disponible |
| Equipo usable | no | [`turnos-agenda-cola-equipo`](../turnos-agenda-cola-equipo/) | **gate-done** 2026-09-28 |
| Call center sesión | no | T5 | disponible |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrar; Clarify **FIRME**) |
| Reservas | BODY n/a · Flyway n/a · UPDATE obs + DELETE al otorgar |
| Universo firmado | [spec.md](spec.md) · inventarios G0 — **FIRME** Camino 1 |
| Fixture | T4 `302674` otorgado; obs G6 sobre `302675` |
| Evidencia | **gate-done** 2026-09-17 |
| Diferidos abiertos | T6 padre (resto). Equipo: [`turnos-agenda-cola-equipo`](../turnos-agenda-cola-equipo/) |
| Próximo paso | N/A este slug — T6 padre |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy north/tabla/south/popup |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Accordion + gear + obs |
| [inventario-validaciones.md](inventario-validaciones.md) | Fechas/horas + vacío |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin ALTER |
| [inventario-geometria.md](inventario-geometria.md) | North 111px · south 150px |

## No es

- Engranaje REASIGNAR de **fila** en Agenda (T6.1 **gate-done**).
- Suspender / reemplazo profesional / vencidos / Historial (T6 padre).
- Avisos (HIS `disabled=true`).
- HOS-APP `turnosonline/turnosAReasignar.xhtml`.
- Equipo usable: [`turnos-agenda-cola-equipo`](../turnos-agenda-cola-equipo/).
