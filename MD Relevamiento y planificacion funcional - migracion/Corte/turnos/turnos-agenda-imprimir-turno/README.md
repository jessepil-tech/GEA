---
title: SDD — T5 hijo · PDF turno (Agenda Imprimir)
description: >-
  Menú gear IMPRIMIR de agenda.xhtml: GET sidecar Turno con idTurno.
  No re-porta el .rptdesign. No porta f_imprime_turno_pac (coseguro).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-agenda-imprimir-turno
---

# turnos-agenda-imprimir-turno

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) T5 **gate-done**.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
BIRT cable: [`regla-migracion-reportes-birt.md`](../../../canon/regla-migracion-reportes-birt.md) § Restricciones.  
Gate (tablero): [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Clarify **FIRME Camino 1** D-TUR-77. Francisco: «ok cobrar esa deuda» (ticket PDF hijo de T5, engranaje Agenda). G6 **PASS** «joya sale bien el pdf».  
Instalación de referencia: Call Center Demo (misma T5). Sin ramas `esClienteX()` en `BBAgenda.actBtnImprimirTurno`.  
**No WAIVE:** HIS Imprimir de fila abre `Turno.rptdesign`.  
Diseño **ya** en Hospital-Reports. Este corte **no** re-migra layout. **No** embeber BIRT. **No** es Consulta Agenda ni ticket AGI.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | — |
| BODY / package | **n/a** (no porta `f_imprime_turno_pac`; print-path BIRT ya en Reports) | n/a |
| Rango Flyway | **n/a** | — |
| Tablas `ts` que escribe | ninguna (GET PDF) | n/a |
| Rama | `dev/t5-imprimir-turno` (Api/Web) · `dest/t5-imprimir-turno` (Migration) | — |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Fila `ts.turno` con paciente, no `RESERVADO` | no | T5 CU (no seed de `turno`) | G6: OTORGADO vivo de agenda |
| Call center sesión | no | T1/T5 | 92001 / 92002 |
| Sidecar Reports | no | Hospital-Reports `:8082` | JDBC = misma PG que Api; `Turno.rptdesign` playbook |
| Coseguro / `f_imprime_turno_pac` | no | hijo | **diferido** — param `montoCoseguro=0` (example Reports) |
| Pack logos centro | no | diferido | `urlLogo=SDLC_VM` (JSON canónico Reports) |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrar; Clarify **FIRME**; G6 **PASS**) |
| Reservas | BODY n/a · Flyway n/a · sin escritura `ts` |
| Universo firmado | [spec.md](spec.md) — **FIRME** Camino 1 |
| Fixture | OTORGADO T5 (no seed); G6 visual **PASS** |
| Evidencia | **gate-done** 2026-09-22 |
| Diferidos abiertos | `turnos-agenda-imprimir-turno-cose` · `turnos-agenda-ficha-imprimir` · pack logos · `TurnoTicket` |
| Próximo paso | N/A este slug — D-TUR-17 / `consultaPreagenda` / alta ATENCION |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin Flyway |

## No es

- Imprimir de Consulta Agenda (`ConsultaAgenda`) ni cola reasignar.
- Ticket AGI G1-d (`NroColaEsperaRecep`).
- `TurnoTicket.rptdesign` (box recepción, `AccionAnterior=Recepcion`).
- Portar `f_imprime_turno_pac` (tmp + FACTURACION + RECEPCIONES).
- Reenviar mail (T7 hijo).
- HOS-APP.
