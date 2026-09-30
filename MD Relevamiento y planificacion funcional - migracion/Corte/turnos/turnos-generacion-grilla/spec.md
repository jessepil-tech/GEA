---
title: Spec — T4 Turnos generación de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-generacion-grilla
---

# Spec — T4 Generación de grilla

Padre: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · corte **T4** · pipeline **A6**.  
[`cortes.md`](../../../relevamiento/relevamiento-turnos/cortes.md) · [`inventario.md`](../../../relevamiento/relevamiento-turnos/inventario.md).

## Problema

T3 dejó franjas configuradas (`grp_prest_tur_*` → horario → días). Legacy materializa
slots **`LIBRE`** en `ts.turno` vía `TS.TURNOS.f_gen_grilla_turnos` (y los borra con
`f_elim_grilla_turnos`). En Api hay DDL `ts.turno` (V31) pero **no** hay comando de
generación: la oferta operativa sigue dependiendo de Oracle o seed AGI OTORGADO.

**Seed OTORGADO ≠ generación de grilla.** Sin T4 no hay paridad de “agenda_turnos”.

## Resultado (FIRME)

1. **Core/Application:** port de generación y eliminación con paridad observable (slots
   `LIBRE`, observaciones, gates T2/T3).
2. **Consulta agendas generadas:** query mensual (`f_consulta_agendas_generadas` o equivalente).
3. **API:** comandos JWT + gate call center (T1); respuesta con lista de observaciones.
4. **UI** bajo menú **TURNOS** → submenú **Agenda turnos** (legacy `agenda_turnos`):
   generar, eliminar, consulta (3 pantallas; paridad UI según canon).
5. **Flyway** mínimo: tablas auxiliares que falten (`turno_a_reasignar`, `hist_turno`,
   `eliminacion_agenda_turnos`, `fecha_feriado` si no existen); **no** levantar todas
   las FKs diferidas de `ts.turno` en v1 salvo las estrictas para grp/hab.
6. IT + smoke + verify anti-gap.

## Clarify — **FIRME** (defaults aceptados · 2026-09-01)

| # | Pregunta | Respuesta | Evidencia / nota |
|---|----------|-----------|------------------|
| 1 | ¿Pipeline previo? | T1 parcial + **T2 gate-done** + **T3 gate-done** (pers+serv). Catálogos ABM fuera. | `f_gen` gate `hab_turnos_*`; input `horario_tur_grp_*` |
| 2 | ¿Capacidades in-scope T4? | **Generar grilla** + **eliminar grilla** + **consulta agendas generadas** (A6). Suspender / quitar suspensión / reemplazo profesional → **T6** (`turnos-ciclo-vida-grilla`). | Menú `agenda_turnos` ids 10821–10824 |
| 3 | ¿Superficies v1? | **`serv` + `pers`** (radio TipoFiltro). **Equipo** → **diferido** D-TUR-17 (UI+SP legacy completos; T3 equipo ABM aún D-TUR-13). | `BBGeneracionGrillaTurnos.TipoFiltro` |
| 4 | ¿Estrategia de port PL/SQL? | **Reimplementación Java** en `TurnosGeneracionPort` + tests golden (fixtures día/grupo/solape) **antes** de UI done. No invocar Oracle en runtime. Observaciones = contrato (textos legacy donde aplique). | `TURNOS.PACKAGE_BODY` ~7200+ LOC |
| 5 | ¿Horario especial (`dia_hora_esp_tur_grp_*`)? | **diferido(`turnos-horarios-especiales`)** — el SP legacy los usa; v1 genera solo franjas semanales T3. Documentar gap en verify (no WAIVE: legacy sí los tiene). | T3 hijo D-TUR-15 |
| 6 | ¿Alcance eliminar grilla? | **In scope:** selección por filtros → borrado/split slots `LIBRE` + histórico mínimo + filas `turno_a_reasignar` cuando hay paciente. **diferido T7:** SMS/mail cancelación (`genera_mails`, params). | `BBEliminarGrillaTurno` · `tmp_turno` |
| 7 | ¿Entry points UI? | **Primario:** menú TURNOS / `generar_agenda`, `eliminar_agenda`, `consulta_agendas_generadas`. **Secundario:** acción post-guardar horario en T3 (`BBHorarioTurnoPersonal`) → **diferido UI** (mismo API; no bloquea gate T4). | xhtml `atencionTurno/*` |
| 8 | ¿Escritura `ts.turno` en DEV? | **Sí** INSERT/DELETE real de slots `LIBRE` en entorno dev-test. Convivencia con seed AGI: smoke usa rango/fecha dedicado o cleanup; no reutilizar IDs demo recepción. FKs `id_grp_prest_tur_*` → levantar en Flyway T4; resto FKs personal/paciente → siguen [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) P-ORA. | V31 + pendientes |
| 9 | ¿Quién muta? | JWT + gate call center (T1). Filtro `personal_adm_tur_serv_centro` en combos → implementar o `diferido(slug)` + seed DEV (misma regla T2/T3). | Configuracion adm turnos |
| 10 | ¿Paridad UI / buscadores? | Reusar buscadores hab (personal-servicio); inventario copy + validaciones BB/MessageBundle **antes** de UI done. Tabla observaciones post-generar obligatoria. | `regla-paridad-ui-legacy` v1.6+ |

**Firma producto:** filas **1–10** aceptadas **2026-09-01** → abrir G0 inventarios + G1 Flyway.

### Detalle Clarify

#### C3 — Modo servicio vs personal

Legacy pasa `id_personal` null → modo servicio; con personal → modo personal. Misma
firma de filtros (centro, servicio, grupo opcional “Todos”, rango fecha/hora, días
sem + feriado).

#### C4 — Observaciones (contrato, no “log técnico”)

`f_gen_grilla_turnos` retorna filas tipo observación (fecha anterior, sin hab, feriado,
sin horario vigente, solape grupos, etc.). UI legacy siempre muestra grid + INFO
`PROCESO_FINALIZADO_CON_EXITO_REVISE_OBSERVACIONES`. T4 debe preservar feedback
usuario (toast + tabla), no solo HTTP 200.

#### C5 — Eliminar y `tmp_turno`

Legacy carga selección en `ts.tmp_turno` antes del SP. Migrado: lista de `id_turno`
en el comando (sin tabla temp Oracle); misma semántica de split por rango hora.

#### C6 — Consulta agendas

Calendario mensual por centro/servicio/(personal) + filtros grupo — solo lectura;
alimenta decisión operativa antes de generar/eliminar.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Generar grilla (serv/pers) | `generacionGrillaTurnos.xhtml` · `BBGeneracionGrillaTurnos` · `f_gen_grilla_turnos` | **In scope** |
| Eliminar grilla (serv/pers) | `eliminarGrillaTurnos.xhtml` · `BBEliminarGrillaTurno` · `f_elim_grilla_turnos` | **In scope** |
| Consulta agendas generadas | `consultaAgendasGeneradas.xhtml` · `f_consulta_agendas_generadas` | **In scope** |
| Generar/equipo | mismo SP modo equipo | **diferido** D-TUR-17 |
| Horario especial en generación | `dia_hora_esp_tur_grp_*` en SP | **diferido** D-TUR-15 |
| Suspender / quitar suspensión grilla | `suspenderGrillaTurnos.xhtml` · `BB*` | **diferido T6** |
| Reemplazo profesional grilla | `reemplazoPersonalGrillaTurnos.xhtml` | **diferido T6** |
| Generar desde T3 horario (popup) | `BBHorarioTurnoPersonal` | API sí / **UI diferido** |
| SMS/mail al eliminar | SP + `genera_mails` | **diferido T7** |
| Reservar / otorgar / agenda | `agenda.xhtml` | **T5** |
| Vista horario grp (consulta) | `horarioGrp*.xhtml` | **diferido** (consulta; puede compartir API T3) |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Comando **generar grilla** pers+serv: parámetros equivalentes al SP (días, feriados, rango, grupo opcional) |
| RF-2 | Gate T2: falla si no hay hab vigente (mensaje legacy) |
| RF-3 | Gate T3: usa horario+días vigentes; respeta `ctd_tur_simultaneo` |
| RF-4 | INSERT `ts.turno` estado `LIBRE`, `sobreturno='N'`, tipo `SERVICIO`/`PERSONAL` |
| RF-5 | Merge/extensión slots adyacentes LIBRE (no duplicar si hay no-LIBRE) |
| RF-6 | Retorno observaciones estructuradas (lista) |
| RF-7 | Comando **eliminar grilla**: selección + split/borrado; `turno_a_reasignar` si paciente |
| RF-8 | Query **consulta agendas** mensual |
| RF-9 | UI 3 pantallas + validaciones BB + inventario copy |
| RF-10 | Flyway tablas auxiliares faltantes + FKs mínimas grp en `turno` |
| NFR-1 | Capas starter; `/api/v1/turnos/grilla/...` (prefijo a confirmar en G1) |
| NFR-2 | Tests golden del algoritmo de generación (casos día, solape, hab, feriado) |
| NFR-3 | Canon DDL `ts`; sin `*_agi` |
| NFR-4 | Paridad UI + buscadores; inventario validaciones UI+API |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-10 | T3 config ≠ T4 generación (slugs separados) |
| D-TUR-16 | T4 = este slug |
| D-TUR-17 | Generación/equipo diferida hasta D-TUR-13 + catálogo equipo |
| D-TUR-18 | Suspender/quitar suspensión/reemplazo grilla → T6 |
| D-TUR-19 | SMS/mail eliminar → T7 |
| D-TUR-20 | Horario especial en algoritmo gen → hijo D-TUR-15 |

## No objetivos

| Ítem | Destino |
|------|---------|
| Reservar / otorgar / liberar | T5 `turnos-agenda-otorgar` |
| Inhibiciones cruzadas en reserva | T5+ |
| Port completo package TS.TURNOS | Solo funciones A6 |
| Levantar todas FKs `ts.turno` | Corte posterior (seed AGI) |
| ABM catálogos / equipo | slugs existentes |

## Evidencia legacy (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/atencionTurno/generacionGrillaTurnos.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/atencionTurno/eliminarGrillaTurnos.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/atencionTurno/consultaAgendasGeneradas.xhtml
Hospital-Legacy/HOSPITAL_2/src/.../BBGeneracionGrillaTurnos.java
Hospital-Legacy/HOSPITAL_2/src/.../BBEliminarGrillaTurno.java
Packages/Turnos/TURNOS.PACKAGE.sql
Packages/Turnos/TURNOS.PACKAGE_BODY.sql
Hospital-Api/.../V31__ts_agi_maestros.sql (ts.turno)
```

Menú: [`relevamiento-his-orientacion/dump-menu-aplicacion.csv`](../../../relevamiento/relevamiento-his-orientacion/dump-menu-aplicacion.csv) — `agenda_turnos` / ids 10820–10825.
