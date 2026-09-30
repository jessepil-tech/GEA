---
title: Spec — Turnos hab buscadores
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-hab-buscadores
---

# Spec — Buscadores hab turnos

Padre: [`turnos-config-hab-horarios/`](../turnos-config-hab-horarios/) (T2).  
Paridad UI: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md).

## Problema

T2 cerró ABM/check/UI con filtros por **ID**. Legacy resuelve el contexto con
buscadores por **nombre** sobre vínculos servicio–centro y personal–servicio.
Sin eso, la pantalla no es operable como en HOSPITAL_2 (ops no conoce IDs).

## Resultado esperado

1. API de búsqueda (JWT, gate call center T1) para:
   - **Servicio–centro** (texto servicio ± filtro centro; lista paginada).
   - **Personal–servicio** (apellido/nombre ± centro/servicio; lista paginada).
2. UI reutilizable (modal/dialog) cableada a:
   - `/configuracion/hab-turnos-serv`
   - `/configuracion/hab-turnos-pers`
3. Flujo: tipear / Buscar → popup resultados → elegir fila → completa labels + ids →
   carga lista hab + habilita Agregar (mismo comportamiento T2 ya hecho).
4. Labels i18n legacy (`buscar_servicio`, columnas Servicio / Centro Atención /
   Profesional, etc.) + criterio diseñador (DS Web).
5. IT + smoke + Paridad UI en verify.

## Clarify — **FIRME** (2026-08-27)

| # | Pregunta | Respuesta | Evidencia |
|---|----------|-----------|-----------|
| 1 | ¿DDL `servicio_centro` / `personal_servicio` en este slug? | **Sí (B0)** — lectura + seed DEV mínimo; sin ABM de vínculos. Canon `ts` desde inventario PG migrado (`pg_ts_columns.csv`: **78** + **21** cols). | Legacy `ServicioCentro` / `PersonalServicio`; Flyway `V38`/`V39` |
| 2 | ¿Incluir sync `atiende_turnos` (D-TUR-11)? | **No** — solo DDL + búsqueda. | pendientes P-ORA-TUR-011 |
| 3 | ¿Alcance UI? | Solo **hab serv** / **hab pers**; dialog buscador **reutilizable**. | xhtml hab |
| 4 | ¿Match exacto 1 fila vs popup? | v1: **siempre lista** en diálogo (simplifica). | `BBBuscador*.buscar` |
| 5 | ¿Filtros avanzados amb/int, solo activos? | v1: texto + filtros id opcionales. Amb/int / solo activos → **D-HAB-BUSC-01**. | `buscadorServicioCentro.xhtml` |
| 6 | ¿Permiso `personal_adm_tur_serv_centro`? | v1: gate T1 (call center). Filtro admin turno → **D-HAB-BUSC-02**. | `BBBuscadorServicioCentro` |

**Labels búsqueda:** join `ts.servicio` / `ts.centro_atencion`; profesional = `ts.persona` (`id_persona = id_personal`, paridad `@Formula` legacy). Seed persona `90001`.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Buscar servicio–centro por nombre | `buscadorServicioCentro.xhtml` · `BBBuscadorServicioCentro` | **In scope** |
| Buscar personal–servicio por nombre | `buscadorPersonalServicio.xhtml` · `BBBuscadorPersonalServicio` | **In scope** |
| Autoselección match único | beans buscador | Opcional / simplificar lista |
| Filtro amb/int / solo activos | xhtml buscador | **diferido(`turnos-hab-buscadores-filtros`)** |
| Combo centro (serv) / combos centro+servicio (pers) | xhtml buscador | **diferido(`turnos-hab-buscadores-filtros`)** |
| Apellido/nombre + cols doc (pers) | xhtml buscador | **diferido(`turnos-hab-buscadores-filtros`)** |
| DDL `servicio_centro` / `personal_servicio` | tablas TS | **B0 in scope** (solo para lectura/seed) |
| Sync `atiende_turnos` | `f_check_hab_turnos` | **Fuera** (D-TUR-11) |
| Buscador equipo | `habTurnosEquipoServ` | **Fuera** (D-TUR-12) |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Flyway: `ts.servicio_centro` + `ts.personal_servicio` (columnas canónicas inventario; FKs a centro/servicio/personal T1) + seed DEV alineado a hab T2 (1001/10/90001) |
| RF-2 | `GET` búsqueda servicio-centro (q, idCentroAte?, page) → idServicio, idCentroAte, labels |
| RF-3 | `GET` búsqueda personal-servicio (q, idCentroAte?, idServicio?, page) → idPersonal, ids, labels |
| RF-4 | Web: diálogo buscador + integración hab-serv / hab-pers (reemplaza inputs ID como entrada primaria; IDs pueden quedar readonly tras selección) |
| RF-5 | Paridad UI: labels msg.*; Buscar compacto; lista con paginator; Aceptar/Cancelar |
| RF-6 | IT + smoke + verify Paridad UI |

## Fuera de alcance

- T3 horarios; ABM de `servicio_centro` / `personal_servicio` (alta vínculo); D-TUR-11 sync; buscador equipo; reescribir todos los buscadores del hospital.

## Criterios de aceptación

1. Desde hab-serv: buscar por texto de servicio → elegir fila → grilla hab del par centro/servicio + Agregar enabled.
2. Desde hab-pers: buscar profesional → elegir → completa centro/servicio/personal + lista.
3. Sin Bearer → 401 en APIs búsqueda.
4. Verify: ninguna capacidad del inventario en silencio.

## Deudas / hijos

| Id | Nota |
|----|------|
| D-TUR-11 | Sync `atiende_turnos` tras tener DDL puente |
| **`turnos-hab-buscadores-filtros`** | **diferido(slug)** — combo centro, amb/int, apellido/nombre separados, combos centro/servicio pers, cols doc. Abrir **después del smoke** del padre. No omitir. |
| D-HAB-BUSC-01 | Absorbido por el hijo filtros (amb/int + resto gaps UI buscador) |
| D-HAB-BUSC-02 | Filtro por `personal_adm_tur_serv_centro` (Clarify del hijo filtros) |
