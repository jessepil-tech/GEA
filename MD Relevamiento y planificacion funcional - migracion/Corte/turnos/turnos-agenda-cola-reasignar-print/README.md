---
title: SDD — T6.2 hijo · PDF cola Reasignación
description: >-
  South Imprimir de turnosAReasignar.xhtml: GET sidecar TurnosAReasignar
  con los mismos filtros que Consultar. No re-porta el .rptdesign.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-print
---

# turnos-agenda-cola-reasignar-print

Padre: [`turnos-agenda-cola-reasignar/`](../turnos-agenda-cola-reasignar/) T6.2 **gate-done**.  
Excel: [`turnos-agenda-cola-reasignar-export/`](../turnos-agenda-cola-reasignar-export/) **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
BIRT cable: [`regla-migracion-reportes-birt.md`](../../../canon/regla-migracion-reportes-birt.md) § Restricciones.  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Clarify **FIRME Camino 1** D-TUR-68. Francisco: «estoy hablando del imprimir de reasignar turno».  
Instalación de referencia: Call Center Demo (misma T6.2). Sin ramas `esClienteX()` en `BBTurnosAReasignar.actBtnImprimir`.  
**No WAIVE:** HIS Imprimir south abre `TurnosAReasignar.rptdesign`.  
Diseño **ya** en Hospital-Reports (playbook 2026-09-08). Este corte **no** re-migra layout. **No** embeber BIRT. **No** es el PDF de Consulta Agenda (`ConsultaAgenda`).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | no (print-path ya en Reports `turnos.p_get_turnos_a_reasignar`; no porta BODY Oracle) | n/a |
| Rango Flyway | **n/a** (dump + package Reports; no `CREATE` ni seed) | — |
| Tablas `ts` que escribe | ninguna (GET PDF) | n/a |
| Rama | `dev/t62-cola-reasignar` (Api/Web/Migration) | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Fila cola | no | T4 CU (no seed) | G6 T6.2: `302675` vigente |
| Combos centro/servicio/personal | no | T5 | disponible |
| Equipo usable | no | D-TUR-17 | combo **disabled**; param vacío |
| Call center sesión | no | T5 | 92001 / 92002 |
| Sidecar Reports | no | Hospital-Reports `:8082` | JDBC = `grupogea-hospital_dev` (misma que Api); print-path instalado 2026-09-17 |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrar; Clarify **FIRME**) |
| Reservas | BODY n/a · Flyway n/a · sin escritura `ts` |
| Universo firmado | [spec.md](spec.md) — **FIRME** Camino 1 |
| Fixture | cola T4 (no seed); G6 PDF con info |
| Evidencia | **gate-done** 2026-09-17 |
| Diferidos abiertos | D-TUR-17 · T6 padre |
| Próximo paso | N/A este slug — D-TUR-17 o T6 padre |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |

## No es

- Imprimir de Consulta Agenda / Turnero (`ConsultaAgenda`).
- Excel south (`turnos-agenda-cola-reasignar-export`).
- Re-portar `TurnosAReasignar.rptdesign`.
- Equipo north usable (D-TUR-17).
