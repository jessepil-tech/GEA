---
title: SDD — Hab. turnos por equipo (D-TUR-12)
description: >-
  Primer tramo de D-TUR-17. ABM hab_turnos_equipo_serv, menú 15413.
  Sin horarios, sin generar grilla, sin filtro Equipo de Agenda.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-hab-equipo
---

# Hab. turnos por equipo (`turnos-hab-equipo`)

**Estado:** **gate-done** 2026-09-23. Clarify **FIRME** 2026-09-22 (Francisco).  
Cobra **D-TUR-12**. Es el primer tramo de **D-TUR-17** (modo equipo): el resto no entra en este techo.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Espejo ya cobrado: T2 [`turnos-config-hab-horarios/`](../turnos-config-hab-horarios/) (serv + pers).  
Instalación de referencia: Call Center Demo. El xhtml y el bean no tienen `esClienteX()`.

Índice (2026-09-22): `--semilla habTurnosEquipoServ` → 3 xhtml / 2 beans / 0 firmas / 0 reportes. Techo ok.  
`--jobs equipo` → 5 jobs de interface laboratorio / ANMAT. **N/A** este circuito (no habilitan turnos).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** (0 firmas `TURNOS.*`; no se porta el package) | writer de `hab_turnos_equipo_serv` **liberado** |
| Rango Flyway | **n/a** (`hab_turnos_equipo_serv` ya en T2; `equipo_serv_centro` ya en V56, vacía) | — |
| Tablas `ts` que escribe | `hab_turnos_equipo_serv` | liberado |
| Rama | `dev/tur-hab-equipo` (Api + Web + Migration) | nombrada; checkout al firmar |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 | no | T1/T2 | vigente |
| `item` `EQDEMO1001` | no | seed mock `padres/item/` | vigente 2026-09-23 |
| `item_equipo` `EQDEMO1001` | no | seed mock `padres/item_equipo/` | vigente 2026-09-23 |
| `equipo_serv_centro` | no | seed mock centro 1001 · servicio 10 · `EQDEMO1001` | vigente para probar el buscador. El ABM del vínculo sigue en [`turnos-equipo-serv-centro/`](../turnos-equipo-serv-centro/) |
| `hab_turnos_equipo_serv` | sí (es el ABM) | acto del CU | no se seedea |

Buscar vacío (o `EQUIPO DEMO`) en HOSPITAL-DEMO / CLINICA MEDICA devuelve una fila y habilita Agregar.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** publicar |
| Reservas | writer `hab_turnos_equipo_serv` liberado · Flyway n/a · BODY TURNOS libre |
| Universo firmado | Camino 1 hab equipo (inventarios de este slug) |
| Fixture | padre `EQDEMO1001` en centro 1001 / servicio 10 |
| Evidencia | [verify-report.md](verify-report.md) · 11 filas verificado |
| Diferidos abiertos | [`turnos-equipo-serv-centro`](../turnos-equipo-serv-centro/) · D-TUR-13 horarios · generación modo equipo · filtros Agenda/historial/suspender/cola · `diferido(auditoria)` · `diferido(perf-volumen)` |
| Próximo paso | D-TUR-13 [`turnos-horarios-equipo`](../turnos-horarios-equipo/) **gate-done** 2026-09-23 |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** |
| [plan.md](plan.md) | G0–G6 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Interacción |
| [inventario-geometria.md](inventario-geometria.md) | Geometría |

**No es** turnos por equipo (D-TUR-13). **No es** generar grilla en modo equipo. **No es** habilitar el combo Equipo de Agenda.
