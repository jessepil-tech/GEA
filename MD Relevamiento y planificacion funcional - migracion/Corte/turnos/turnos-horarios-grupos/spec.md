---
title: Spec — T3 Turnos grupos + horarios
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-horarios-grupos
---

# Spec — T3 Grupos + horarios

Padre: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · corte **T3**  
[`cortes.md`](../../../relevamiento/relevamiento-turnos/cortes.md) · pipeline A5.

## Problema

Sin grupos de prestación + horario + días, legacy no puede materializar oferta
(`f_gen_grilla_turnos`). T2 dejó `hab_turnos_*` vigentes; en Api **no** hay aún
`grp_prest_tur_*` / `horario_tur_grp_*` / `dia_horario_*`.  
**Seed OTORGADO ≠ configuración de franjas.**

## Resultado (propuesto)

1. DDL canónico (cols = `pg_ts_columns.csv`) para cadena **pers** y **serv**:
   - `grp_prest_tur_{pers|serv}` (8 / 7)
   - `prest_grp_prest_tur_{pers|serv}` (10 / 10)
   - `horario_tur_grp_{pers|serv}` (6 / 6)
   - `dia_horario_tur_grp_{pers|serv}` (9 / 6 — asimetría legacy)
2. Equipo: **DDL + sin ABM** (paridad T2 / D-TUR-12) o diferir DDL si se acepta C2b.
3. Seed DEV: al menos un grupo+prestaciones+horario+días por superficie (pers y/o serv) sobre hab T2 (1001/10/90001).
4. API (JWT + gate call center T1): list/CRUD mínimo de la cadena; sin generación.
5. UI bajo menú **TURNOS** (padre `MENU_APLICACION`; URL puede vivir en `/configuracion/…` como hab): pantallas alineadas a `turnosPersonal/*` y `turnosServicios/*` (grupo → prestaciones → horario → días). Paridad UI según [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md); orientación [`regla-paridad-orientacion-visual.md`](../../../canon/regla-paridad-orientacion-visual.md).
6. IT + smoke.

## Clarify — **FIRME** (defaults aceptados · 2026-08-28)

| # | Pregunta | Respuesta propuesta | Evidencia / nota |
|---|----------|---------------------|------------------|
| 1 | ¿Pipeline previo? | T1 parcial + T2 gate-done + centro/servicio DDL/seed. ABM maestros **fuera**. | cortes T3 prerreq T2 |
| 2 | ¿Superficies v1? | **`pers` + `serv`**. Equipo = DDL (opcional) sin ABM/UI — **D-TUR-13**. | xhtml `turnosPersonal` / `turnosServicios` / `turnosEquipo` |
| 3 | ¿Cadena mínima in-scope? | Grupo → prestaciones del grupo → horario (vigencia) → días de la semana. | Input de `f_gen_grilla_turnos` |
| 4 | ¿Inhibiciones? | **diferido(`turnos-horarios-inhibiciones`)** — tablas `horario_inh_tur_*` + UI `inhibicionTurno*`. | Cortes las listan en T3; slice v1 no las omite en silencio |
| 5 | ¿Horario especial / ocupación conv-plan / múltiple? | **diferido** slugs hijos (ver inventario). | `horarioEspecial*`, `ocupacionTur*`, `GRP_PREST_TUR_MULTIPLE` |
| 6 | ¿UI dónde? | **Menú TURNOS** (O1 / orientación). Ruta Angular puede quedar `/configuracion/horarios-turnos-*` (como hab). `atencionTurno/horarioGrp*` = vista ops → **diferido** o reutilizar misma API más adelante. | Beans `BBHorarioTurno*` vs `BBHorarioGrp*`; [`paridad-orientacion-web/`](../../plataforma/paridad-orientacion-web/) |
| 7 | ¿Escritura? | **DDL + seed + GET + POST/PUT/DELETE** mínimo. Solo lectura rompería preparación T4 en DEV. | Mismo criterio T2 C1 |
| 8 | ¿Quién muta? | JWT + gate call center (T1). Filtro `personal_adm_tur_serv_centro` → diferido. | Igual T2 |
| 9 | ¿IDs? | Generar con mecanismo canónico Api (`NextId` / `sec_id_tabla` según tablas). No inventar secuencias fuera de inventario. | Hibernate `CustomIdGenerator` legacy |
| 10 | ¿Paridad UI / buscadores? | Chrome + labels; reusar buscadores hab donde el xhtml abre popup servicio/personal. Gaps dialog → hijo o ampliar `turnos-hab-buscadores*`. | regla-paridad-ui |

**Firma producto:** filas **1–5 + 7** (y C2a DDL equipo sin ABM) **aceptadas** 2026-08-28 → implement G1+.

### Detalle Clarify

#### C2 — Equipo

Sin maestro equipo en Api, ABM imposible. Opciones:
- **C2a (default):** DDL `grp/prest/horario/dia` equipo en el mismo Flyway; sin Resource/UI.
- **C2b:** diferir DDL equipo a D-TUR-13 hasta existir catálogo.

#### C3 — Orden de datos

1. Existe hab vigente (T2).  
2. Alta **grupo** (centro+servicio[+personal]).  
3. Alta **prestaciones** del grupo (`prestacion` V31 seed).  
4. Alta **horario** con vigencia.  
5. Alta **días** (nro día + hora desde/hasta; pers tiene cols extra reserva).

#### C4 — Inhibiciones

Reducen oferta; no bloquean tener un horario mínimo para T4. Diferir con slug evita silence y mantiene T3 v1 revisable.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| ABM grupo prestación turno personal | `grupoPrestacionesTurnoPersonal.xhtml` · `GrpPrestTurPers` | **In scope** |
| Prestaciones del grupo personal | `prestacionGrupoPrestacionesTurnoPersonal.xhtml` | **In scope** |
| Horario + días personal | `horarioTurnoPersonal.xhtml` · `HorarioTurGrpPers` / `DiaHorario*` | **In scope** |
| ABM grupo / prest / horario / días servicio | `turnosServicios/grupo*|prestacion*|horario*` | **In scope** |
| Equipo (grupo/horario/días) | `turnosEquipo/*` | **DDL**; ABM **diferido** D-TUR-13 |
| Inhibiciones pers/serv/equipo | `inhibicionTurno*.xhtml` · `HorarioInhTur*` | **diferido(`turnos-horarios-inhibiciones`)** |
| Horario especial | `horarioEspecialTurno*.xhtml` | **diferido(`turnos-horarios-especiales`)** |
| Ocupación conv/plan | `ocupacionTur*.xhtml` | **diferido** (T5-ish / hijo) |
| Grupo múltiple | `GRP_PREST_TUR_MULTIPLE` | **diferido** |
| Vista `horarioGrpPers/Serv` (atencionTurno) | `BBHorarioGrp*` | **diferido** o misma API post-gate |
| Generación grilla | `f_gen_grilla_turnos` | **T4** |
| Agenda / otorgar | `agenda.xhtml` | **T5** |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Flyway: tablas cadena pers+serv (cols inventario); equipo según C2 |
| RF-2 | FKs a `centro_atencion`, `servicio`, `personal`, `prestacion`, hab donde aplique |
| RF-3 | Seed DEV usable post-T2 |
| RF-4 | API list/CRUD grupo + prestaciones + horario + días (pers y serv) |
| RF-5 | UI CONFIGURACION: al menos flujo pers **o** serv completo en v1 UI (ambos API); ideal ambos UI |
| NFR-1 | Capas starter; `/api/v1/turnos/...` |
| NFR-2 | No ensanchar AGI / seed OTORGADO |
| NFR-3 | Canon DDL `ts`; sin `*_agi` |
| NFR-4 | Paridad UI + buscadores sin silencios |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-10 | T2 hab; T3 horarios = este slug (no mezclar) |
| D-TUR-13 | Equipo: DDL sin ABM (o diferir DDL si firma C2b) |
| D-TUR-14 | Inhibiciones = hijo `turnos-horarios-inhibiciones` |
| D-TUR-15 | Horario especial = hijo `turnos-horarios-especiales` |

## No objetivos

| Ítem | Destino |
|------|---------|
| `f_gen_grilla_turnos` / eliminar grilla | T4 `turnos-generacion-grilla` |
| Agenda / reservar / otorgar | T5 |
| Sync `atiende_turnos` | D-TUR-11 |
| ABM centro/servicio/prestación catálogo | `catalogo-abm-*` |
| ABM hab equipo | D-TUR-12 |

## Criterios de aceptación

1. `\d` columnas coincide con inventario para tablas in-scope.
2. IT: crear cadena mínima → listar días; 401 sin JWT.
3. UI usable con JWT post-T1/T2 (al menos una superficie completa).
4. Inventario: done / diferido(slug) / WAIVE — sin silencios.
5. Sin regresión T1/T2 / AGI.

## Evidencia paths

- Config: `HOSPITAL_2/.../configuracion/servicio/turnosPersonal/*`, `turnosServicios/*`, `turnosEquipo/*`
- Beans: `BBHorarioTurnoPersonal`, `BBHorarioTurnoServicio`, …
- DTOs/HBM: `GrpPrestTur*`, `PrestGrpPrestTur*`, `HorarioTurGrp*`, `DiaHorarioTurGrp*`
- Inventario cols: [`pg_ts_columns.csv`](../../../relevamiento/inventario-ddl-oracle-pg/pg_ts_columns.csv)
