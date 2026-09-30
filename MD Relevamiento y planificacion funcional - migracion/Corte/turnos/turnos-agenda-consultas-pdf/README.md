---
title: SDD — T5.5 hijo · PDF consulta agenda con filas
description: >-
  G6 visual: sidecar ConsultaAgenda con las mismas filas/valores
  que la grilla. Cable T5.5 hecho; contenido = este hijo.
version: 0.3.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-pdf
---

# turnos-agenda-consultas-pdf

Padre: [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) T5.5.  
Excel: [`turnos-agenda-consultas-export/`](../turnos-agenda-consultas-export/) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
BIRT cable: [`regla-migracion-reportes-birt.md`](../../../canon/regla-migracion-reportes-birt.md) § Restricciones (DDL / print-path).  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md) — **gate-done** 2026-09-15.

Clarify **FIRME Camino 1** D-TUR-62. G6 **PASS** (Francisco: «ok ahora se está visualizando información en los pdf»).  
Instalación de referencia: Call Center Demo (dump piloto; misma T5.5). Sin ramas `esClienteX()` en el print-path.  
**No WAIVE:** HIS `actBtnImprimir` muestra turnos en el PDF.  
Cable T5.5 **hecho:** GET `/api/v1/turnos/agenda/consulta/imprimir.pdf` → `reportId=ConsultaAgenda`. **No** re-migrar layout. **No** embeber BIRT.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (print-path ya en Reports `packages-pg`; este corte no porta BODY Oracle) | liberado |
| Rango Flyway | **V56** | liberado (cerrado) |
| Tablas `ts` que escribe | `turno_vencido`, `equipo_serv_centro`, `te_persona` (`IF NOT EXISTS`, vacías) | liberado |
| Rama | `dev/dev` | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.turno` hoy (grilla = PDF) | no | dump / T5 | disponible |
| `ts.turno_vencido` | sí (DDL vacío; no job T6) | Flyway V56 | disponible (0 filas) |
| `ts.equipo_serv_centro` | sí (DDL vacío; no ABM) | Flyway V56 | disponible (0 filas) |
| `ts.te_persona` | sí (DDL vacío) | Flyway V56 | disponible (0 filas) |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrado) |
| Reservas | liberadas |
| Universo firmado | [spec.md](spec.md) · [inventario-ddl-gaps.md](inventario-ddl-gaps.md) |
| Fixture | resuelto |
| Evidencia | 9 filas **verificado** ([verify-report.md](verify-report.md)) |
| Diferidos abiertos | T6 vencidos · D-TUR-17 |
| Próximo paso | ninguno en este corte |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger — PASS 2026-09-15 |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | `turno_vencido` + `equipo_serv_centro` |

## Hallazgo

Tras V56 el package **corre**. El PDF vacío era params (default BIRT `44983` + float blank→`0`), no falta de filas en `ts.turno`. Detalle de DoR del cable: canon BIRT citado arriba. Job vencidos = T6.

## No es

- Re-portar `ConsultaAgenda.rptdesign` (salvo quitar default 44983).
- Excel POI.
- Equipo usable (D-TUR-17).
- `f_migra_turno_vencido`.
