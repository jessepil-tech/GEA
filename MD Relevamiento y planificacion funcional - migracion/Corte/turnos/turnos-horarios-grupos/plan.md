---
title: Plan — T3 Turnos grupos + horarios
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-horarios-grupos
---

# Plan — T3

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Flyway Api V41+: cadena `grp` → `prest_grp` → `horario` → `dia` (pers+serv); equipo según Clarify C2 |
| Core | `TurnosHorariosPort` (o ports por agregado si el starter lo pide) |
| Application | CQRS list/create/update/delete por entidad de la cadena |
| Infrastructure | JDBC `ts.*`; seed sobre hab T2 |
| Presentation | `/api/v1/turnos/horarios/...` o `/grupos/...` (definir en G1) |
| Web | Páginas CONFIGURACION; reusar buscadores hab |
| Job | N/A |

Orden: firma Clarify → Flyway+seed → API cadena → UI → IT/smoke → verify + matriz.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME |
| G1 | Flyway tablas + FKs + seed |
| G2 | API pers (grupo→días) + IT |
| G3 | API serv + IT |
| G4 | UI al menos una superficie completa; segunda o diferido UI documentado |
| G5 | verify + backlog/matriz/pendientes |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Scope creep T4 generación | Rechazar en PR; slug T4 |
| Slice gigante (6+ xhtml × 3 superficies) | Pers+serv API; UI por superficie; equipo/inh/especial diferidos |
| Asimetría cols `dia_horario_*` pers vs serv | Copiar inventario; no “igualar” |
| IDs / `sec_id_tabla` | Seguir patrón Api existente |
| Prestaciones sin ABM | Solo seed V31 + pickers existentes |

## Dependencias

- T2 gate-done (`hab_turnos_*`, buscadores)
- `ts.centro_atencion` / `servicio` / `personal` / `prestacion` / `servicio_centro`
- Relevamiento T0

## Fuera del plan de código T3 v1

T4–T7; inhibiciones; horario especial; ocupación; múltiple; ABM equipo; ABM catálogos.
