---
title: Plan — T2 Turnos habilitación
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-config-hab-horarios
---

# Plan — T2

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Flyway Api V3x: 3 tablas `hab_turnos_*` desde inventario — **no** rediseñar |
| Core | `TurnosHabPort` (list/create/update/checkVigencia) |
| Application | Queries list + commands create/update/check |
| Infrastructure | JDBC `ts.*`; seed DEV alineado T1 (centro/servicio/personal) |
| Presentation | `/api/v1/turnos/hab/...` |
| Web | Página bajo TURNOS (post picker CC); no mezclar con agenda |
| Job | Check vía comando API; Quartz opcional |

Orden: Flyway + seed → check + GET → POST/PUT → UI → IT/smoke.

## Cortes internos

| Corte | Exit |
|-------|------|
| H0 | Clarify firmado |
| H1 | Flyway 3 tablas + FKs |
| H2 | Seed + check vigencia |
| H3 | GET/POST/PUT serv+pers + IT |
| H4 | UI lista/alta |
| H5 | verify + matriz/pendientes |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Scope creep T3 horarios | Rechazar en PR; slug hijo |
| `f_check` toca tablas no migradas | D-TUR-11: solo `hab_*.vigente` |
| Equipo sin maestro | D-TUR-12: DDL sin ABM |
| PK compuesta + historial vigencias | Paridad legacy: varias filas por clave con distintas `fecha_vigencia`; check elige la vigente |
| Confundir con T1 personal | Hab es config de oferta; T1 es identidad operador |

## Dependencias

- T1 gate-done (personal, call center, seed admin)
- `ts.centro_atencion` / `ts.servicio` (V28/V30)
- Relevamiento T0

## Fuera del plan de código T2

T3–T7; ABM equipo; sync `atiende_turnos` en tablas puente no migradas.
