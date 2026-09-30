---
title: SDD — Turnos por equipo (D-TUR-13)
description: >-
  Horarios de equipo: grupo, prestaciones y horario con días.
  Sin especial, sin inhibición, sin ocupación, sin generar grilla.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo
---

# Turnos por equipo (`turnos-horarios-equipo`)

**Estado:** **gate-done** 2026-09-23. Clarify **FIRME** (ealbo, «ok firme»). Rama `dev/tur-horarios-equipo`.  
Cobra **D-TUR-13**. Es el espejo de T3 ([`turnos-horarios-grupos/`](../turnos-horarios-grupos/)) para equipo, después de la hab ([`turnos-hab-equipo/`](../turnos-hab-equipo/)).  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Instalación de referencia: Call Center Demo. Los beans de `turnoEquipo` no tienen `esClienteX()`.

Índice (2026-09-23): `--semilla turnosEquipo` → 3 xhtml / 2 beans / 0 firmas / 0 reportes. Techo ok.  
La semilla es el cascarón. El menú lateral navega a otras hojas: entran las de la cadena T3 y el resto queda diferido.  
`--jobs equipo` → 5 jobs de interface laboratorio / ANMAT. **N/A** este circuito (no arman el horario).

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **n/a JDBC** (0 firmas en el índice; `Turnos.insert*` es CRUD de negocio, igual que T3) | writer de las cinco tablas **liberado**. BODY TURNOS sigue libre |
| Rango Flyway | **n/a** (las tablas ya están en el dump `ts`) | — |
| Tablas `ts` que escribe | `grp_prest_tur_equipo`, `prest_grp_prest_tur_equipo`, `horario_tur_grp_equipo`, `dia_horario_tur_grp_equipo`, `reserva_tur_equipo_plan_conv` | liberado |
| Rama | `dev/tur-horarios-equipo` (Api + Web + Migration) | local, sin publicar |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 | no | T1/T2 | vigente |
| `equipo_serv_centro` `EQDEMO1001` | no | seed mock de la hab | vigente |
| `hab_turnos_equipo_serv` 2026-09-23 vigente S | no | acto del CU de la hab | vigente |
| `prestacion` `CONS` | no | seed T3 | vigente (1 fila) |
| `grp_prest_tur_equipo` y el resto de la cadena | sí (la deja este corte) | acto del CU | no se seedea. Hoy: grupo id=2, horario id=1, prestación `CONS` |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · **gate-done** 2026-09-23 |
| Reservas | cinco tablas liberadas · Flyway n/a · BODY TURNOS libre |
| Universo firmado | cascarón + datos + grupo + prestaciones + horario/días + reserva del lápiz |
| Fixture | padres vigentes. La cadena no se seedea |
| Evidencia | [verify-report.md](verify-report.md) · ledger 1–9 verificado · **PASS** |
| Diferidos abiertos | horario especial · inhibición · ocupación conv/plan · generar grilla modo equipo [`turnos-grilla-equipo`](../turnos-grilla-equipo/) · `diferido(auditoria)` · `diferido(perf-volumen)` |
| Próximo paso | ninguno en este slug. Los diferidos tienen su carpeta |

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

**No es** horario especial, inhibición ni ocupación. **No es** generar la grilla en modo equipo. **No es** el vínculo `equipo_serv_centro`.
