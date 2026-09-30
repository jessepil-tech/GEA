---
title: Spec — T2 Turnos habilitación hab_turnos_*
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-config-hab-horarios
---

# Spec — T2 Habilitación turnos

Padre: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · corte **T2**  
[`cortes.md`](../../../relevamiento/relevamiento-turnos/cortes.md) · pipeline A4.

## Problema

Sin filas vigentes en `HAB_TURNOS_*`, legacy no habilita oferta (`atiende_turnos`) ni tiene base
para horarios/generación. En Api solo existen maestros T1 + `ts.turno` (consumo AGI).  
**Seed OTORGADO ≠ configuración de habilitación.**

## Resultado

1. DDL canónico `ts.hab_turnos_serv_centro`, `ts.hab_turnos_pers_serv`, `ts.hab_turnos_equipo_serv`
   (columnas = `pg_ts_columns.csv`: 25 / 26 / 26).
2. Seed DEV: al menos una hab **serv** y una **pers** vigentes (centro/servicio/personal T1).
3. API (JWT): listar hab por centro/servicio; alta/edición mínima serv+pers; comando **check vigencia**
   (paridad núcleo de `f_check_hab_turnos` sobre las 3 tablas hab).
4. UI bajo **CONFIGURACION**: dos pantallas (serv / profesional) — Buscar → lista → Agregar/Editar/Eliminar
   (paridad `habTurnos*.xhtml`). Sin agenda. Equipo diferido.
5. IT + smoke.

## Clarify — **FIRME** (defaults aceptados · 2026-08-27)

| # | Pregunta | Respuesta propuesta | Evidencia / nota |
|---|----------|---------------------|------------------|
| 1 | ¿Alcance escritura? | **DDL + seed + GET + POST/PUT mínimo** (serv + pers). No solo lectura. | Legacy tiene ABM `BBHabTurnos*`; diferir “solo sync” rompería ops DEV |
| 2 | ¿Qué superficies v1? | **`hab_turnos_serv_centro` + `hab_turnos_pers_serv`**. Equipo = DDL + check, **ABM/UI diferido** (necesita maestro equipo). | Tres xhtml; equipo depende `cod_item_equipo` |
| 3 | ¿Vigencia cómo? | Comando `POST …/hab/check` (o job opcional) que replica lógica de `f_check_hab_turnos` **sobre hab_***: pone `vigente='N'` y marca `S` la fila con max `fecha_vigencia` aún válida. | Package TURNOS ~14213 |
| 4 | ¿Side-effect `atiende_turnos` en `servicio_centro` / `personal_servicio` / `equipo_serv_centro`? | **Diferido** hasta DDL de esas tablas en Api (hoy no están en Flyway). Documentar en pendientes. Check T2 solo actualiza `hab_*.vigente`. | Mismo `f_check_hab_turnos` líneas 14255+ |
| 5 | ¿Quién puede mutar? | JWT autenticado + (futuro) filtro `personal_adm_tur_serv_centro`. v1: mismo gate que T1 (usuario con call center). Permiso fino → diferido. | `BBPersonalAdministraTurno` |
| 6 | ¿Flags mail/SMS/WA / topes? | **Persistir columnas** (paridad DDL). UI popup = campos de `habTurnos*.xhtml` (vigencia, mails/SMS/WA, hs mín., días visualiza, topes, permite ate. amb.). | 25–26 cols |
| 7 | ¿T3 en este slug? | **No.** Horarios/grupos = hijo `turnos-horarios-grupos` o ampliar este slug solo tras gate T2. | cortes.md |

**Firma producto:** filas **1–4** **aceptadas** 2026-08-27 → implement H1+.

### Detalle Clarify

#### C1 — Lectura vs ABM

Defaults T1 fueron “DDL + gate lectura”. Aquí la hab **es** config operativa: sin alta, DEV no puede preparar T3.  
ABM v1 = crear/editar vigencia + `vigente` derivado por check (no editar `vigente` a mano salvo override documentado).

#### C2 — Equipo diferido

`HAB_TURNOS_EQUIPO_SERV` entra en Flyway (RF-1) y en el check (RF-3) para no dejar la tabla huérfana, pero **sin** pantalla ni POST hasta existir catálogo equipo / `equipo_serv_centro`.

#### C3 — Check vs job

Legacy: `CheckHabTurnosJob` llama `f_check_hab_turnos`.  
T2: endpoint/comando invocable (smoke + ops); Quartz job opcional si `features.enable-background-jobs` — no obligatorio para gate.

#### C4 — `atiende_turnos`

El package también actualiza `servicio_centro` / `personal_servicio` / `equipo_serv_centro`. Esas tablas **no** están en Flyway Api → **diferir** sync con slug/nota en `pendientes-solo-oracle.md`. No inventar tablas `*_agi`.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| ABM hab servicio-centro | `habTurnosServCentro.xhtml` · `BBHabTurnosServCentro` | **In scope** (mínimo) |
| ABM hab personal-servicio | `habTurnosPersServ.xhtml` · `BBHabTurnosPersServ` | **In scope** (mínimo) |
| ABM hab equipo-servicio | `habTurnosEquipoServ.xhtml` · `BBHabTurnosEquipoServ` | **DDL + check**; ABM/UI **diferido** |
| Job / check vigencia | `CheckHabTurnosJob` · `f_check_hab_turnos` | **Check sobre hab_***; sync `atiende_turnos` **diferido** |
| Admin quién configura | `personalAdministraTurno` | DDL T1; filtro UI **diferido** |
| Horarios / grupos | config turnos* | **T3** |
| Generación grilla | `f_gen_grilla_turnos` | **T4** |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Flyway: 3 tablas `hab_turnos_*` — **mismas columnas** que inventario |
| RF-2 | PK + FKs a `centro_atencion`, `servicio`, `personal` (pers); equipo sin FK a maestro inexistente o diferida documentada |
| RF-3 | Comando check vigencia paridad núcleo `f_check_hab_turnos` (solo `hab_*`) |
| RF-4 | `GET` listado filtrable centro/servicio/(personal) |
| RF-5 | `POST`/`PUT`/`DELETE` mínimo serv + pers |
| RF-6 | UI CONFIGURACION: `/configuracion/hab-turnos-serv` + `/configuracion/hab-turnos-pers` (flujo Buscar → lista → Agregar/Editar/Eliminar) |
| NFR-1 | Capas starter; prefijo `/api/v1/turnos/...` |
| NFR-2 | No ensanchar AGI / seed OTORGADO |
| NFR-3 | No inventar `public` / `*_agi` |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-10 | T2 = hab config; T3 horarios es hijo/corte aparte |
| D-TUR-11 | Check T2 no requiere aún `servicio_centro.atiende_turnos` (diferido con DDL de esas tablas) |
| D-TUR-12 | Equipo: DDL+check ahora; ABM cuando exista maestro equipo en Api |

## No objetivos

| Ítem | Destino |
|------|---------|
| `grp_prest_tur_*`, `horario_tur_grp_*`, días, inhibiciones | T3 |
| `f_gen_grilla_turnos` / eliminar grilla | T4 |
| Agenda / otorgar | T5 |
| SMS/mail reales al habilitar | T7 / flags solo persistidos |
| ABM equipo | diferido (D-TUR-12) |
| Sync `atiende_turnos` en servicio_centro / personal_servicio | diferido (D-TUR-11) |
| Buscadores por nombre (hab UI) | hijo [`turnos-hab-buscadores/`](../turnos-hab-buscadores/) |

## Criterios de aceptación

1. `\d` columnas coincide con inventario (25/26/26).
2. IT: listar; crear hab; check deja exactamente una `vigente=S` por clave de negocio en ventana.
3. UI lista/alta usable con JWT post-T1.
4. AGI/colas/anunciador sin regresión.
5. Capacidades de inventario: done / diferido(slug) / WAIVE — sin silencios.

## Evidencia paths

- `HOSPITAL_2/.../habTurnos{ServCentro,PersServ,EquipoServ}.xhtml`
- `BBHabTurnos*.java`
- `SCHEDULER/.../CheckHabTurnosJob.java`
- `Package TURNOS` · `f_check_hab_turnos`
- Inventario: `docs/sdd/inventario-ddl-oracle-pg/pg_ts_columns.csv`
