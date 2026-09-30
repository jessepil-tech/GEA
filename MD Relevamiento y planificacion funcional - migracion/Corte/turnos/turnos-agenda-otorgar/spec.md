---
title: Spec — T5 Turnos agenda / otorgar
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-otorgar
---

# Spec — T5 Operación agenda

Padre: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · corte **T5** · pipeline **A7**.  
[`cortes.md`](../../../relevamiento/relevamiento-turnos/cortes.md) · [`inventario.md`](../../../relevamiento/relevamiento-turnos/inventario.md).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md) · proceso [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).

## Problema

T4 materializa oferta `ts.turno` **`LIBRE`**. AGI solo **consume** `OTORGADO`.  
En Api **no** hay comando de reservar/otorgar/liberar ni query de grilla del día: la operación diaria sigue en Oracle (`BBAgenda` → `TS.TURNOS.f_get_grilla_dia` / `f_reserva_turno_pac` / `f_otorga_turno_pac`).

**Seed OTORGADO AGI ≠ agenda HIS.** Sin T5 el módulo TURNOS no otorga.

## Resultado (FIRME)

1. **Core/Application:** port de grilla día + reserva + otorgar + tomar (lock A) + liberar + sobreturno, con paridad de estados y `hist_turno` en otorga/libera.
2. **API JWT** + gate call center (T1).
3. **UI** bajo menú **TURNOS** → hoja **Turnero / Agenda** (`/turnos/agenda`): filtros paciente/convenio/prestación + calendario + grilla + acciones.
4. **Flyway** mínimo: maestros leídos por el SP que falten (`ctrl_turnos_pac` si se cobra el tope); **sin** tablas `tmp_turno*` Oracle.
5. IT + golden + smoke + e2e + verify anti-gap.

## Clarify — **FIRME** (2026-09-07)

Filas 1–16 aceptadas. **Lock sesión:** opción A (**D-TUR-28**). G0 inventarios completados.

| # | Pregunta | Respuesta FIRME | Evidencia / nota |
|---|----------|-----------------|------------------|
| 1 | ¿Pipeline previo? | T1 parcial + **T2–T4 gate-done**. Paciente/convenio/prestación DDL V31. Call center T1. | `BBInicioTurnos` · hab T2 · slots T4 |
| 2 | ¿Capacidades in-scope T5? | **Consultar grilla día** + **reservar** + **otorgar** + **tomar/lock** + **liberar** + **sobreturno**. | `f_get_grilla_dia` · `f_reserva_turno_pac` · `f_otorga_turno_pac` · `f_tomar_turno_pac` · `f_libera_turno_pac` · `f_reserva_sobreturno_pac` |
| 3 | ¿Superficies v1? | **serv + pers**. **Equipo** → **diferido** D-TUR-17 (alineado T3/T4). | `BBAgenda` filtros centro/serv/pers/equipo |
| 4 | ¿Estrategia PL/SQL? | **Reimplementación Java** `TurnosAgendaPort` + golden **antes** de UI done. No Oracle en runtime. Sin `tmp_turno` persistida: DTO/lista. | `TURNOS.PACKAGE_BODY` · T4 mismo patrón |
| 5 | ¿Lock sesión / timeout reserva? | **Opción A — paridad legacy (FIRME 2026-09-07):** `f_tomar_turno_pac` renueva `session_id` + `fecha_session` en `RESERVADO`; poll UI **59 s** en popup info turno (`infoTurno.xhtml`); rechazo si otro operador tiene lock **< 3 min** (*“El turno esta siendo tomado por otro usuario”*); reserva también setea lock al pasar a `RESERVADO`; `f_liberar_turno_reservado` al **consultar grilla** + `POST …/expirar-reservas` (ops): `RESERVADO` > **3 min** → libera; sobreturno `RESERVADO` > **30 min** → DELETE. `session_id` Api = id operador JWT (estable por login). Job Quartz **diferido** (D-TUR-27). | `TURNOS.PACKAGE_BODY` ~6145 · `infoTurno` poll · V31 cols |
| 6 | ¿Inhibición cruzada al reservar? | **In scope** (parte de `pp_reserva` / `pp_inhibe`). Golden. No es T6 suspender. | `INHIBIDO` + `id_turno_inhibe` |
| 7 | ¿Entry UI? | **`/turnos/agenda`** bajo TURNOS (reemplaza `pending('Turnero')`). Prerrequisito: picker call center `/turnos/inicio`. **No** clonar HOS-APP `inicioAgenda.xhtml`. | O1 `turnero` 15001; HIS `agenda.xhtml`; puente 15003 fuera |
| 8 | ¿Paridad UI / Gate arranque? | Inventarios copy + validaciones **antes** de template. Geometría: north filtros (paciente/convenio/plan/afiliado + prestación/centro/servicio/profesional) + calendario + grilla; west menú turnero (Agenda / Sobreturno); dialogs infoTurno / motivo libera / sobreturno. Toast + modal. | `regla-paridad-ui-legacy` v1.10 · skill `gate-ui-arranque` |
| 9 | ¿Elegibilidad WS / coseguro / CTA CTE? | **diferido:** WS real **P-ORA-010**; popups pagar/saldo/prescripción compleja → hijo `turnos-agenda-elegibilidad-cobros`. v1: convenio+plan+afiliado requeridos; stub/seed como AGI. | `validarElegibilidad` · `$popUpInfoPagar` |
| 10 | ¿West paciente (ficha, recetas, próximos)? | **diferido** `turnos-agenda-ficha-paciente`. v1: buscador paciente + datos mínimos en north. | `asignacionTurnos.xhtml` tab paciente |
| 11 | ¿Repetidos / múltiples / pre-agenda? | **diferido** hijos (ya en inventario T0). | `f_reservar_turnos_repetidos` · `f_reserva_turno_multiple_pac` · `pre_agenda_turno` (package ATENCION) |
| 12 | ¿Reasignar desde agenda? | **Hijo T6.1 gate-done** 2026-09-10 [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/). Liberar simple **sí** es T5. Cola / suspender / reemplazo → padre T6. | `actBtnReemplazarTurno` · D-TUR-26 |
| 13 | ¿Tope `ctrl_turnos_pac`? | **Read in-scope** si G1 trae DDL; ABM `ctrlCtdMaxTurnosPac.xhtml` → **diferido** `turnos-ctrl-ctd-max-pac`. Si G1 no cobra tabla, flag de grilla queda diferido (no silencio). | `cantidadMaxTurnoExcedida` · `pp_ctrl_turnos_pac` |
| 14 | ¿hist_turno? | **In scope** en otorga y libera (no solo-RESERVADO). Tabla V43. | `pf_hist_turno` |
| 15 | ¿Viaje Playwright? | **e2e-migrado** (3 viajes): consultar grilla; reservar+otorgar; liberar. Fixture PG nombrado. Legacy HIS **no**. Mocks ≠ G6. | `regla-playwright-migracion.md` |
| 16 | ¿Quién muta? | JWT + call center T1. Combos filtrados por hab T2 / adm servicio (misma regla T4). | `id_call_center` en SP |

**Firma producto:** filas 1–16 aceptadas **2026-09-07** → G0 done; siguiente **G1** Flyway.

### Detalle Clarify

#### C2 — Flujo HIS (núcleo)

```
/turnos/inicio (call center)
  → /turnos/agenda
      consultar → f_get_dias_disponibles + f_get_grilla_dia
      ASIGNAR   → f_reserva_turno_pac → popup info turno
      OTORGAR   → f_otorga_turno_pac
      unload    → f_tomar_turno_pac
      LIBERAR   → f_libera_turno_pac (motivo)
      SOBRETURNO→ f_reserva_sobreturno_pac → mismo otorga
```

Estados: `LIBRE` → `RESERVADO` → `OTORGADO`. Liberar vuelve a `LIBRE` (o DELETE si sobreturno). AGI sigue marcando `RECEPCIONADO` (otro módulo).

#### C5 — Lock sesión (opción A · FIRME)

Paridad `f_tomar_turno_pac` + `f_liberar_turno_reservado`:

| Pieza | Legacy | Migrado |
|-------|--------|---------|
| Al reservar | `pp_reserva` setea `session_id` + `fecha_session` | Igual en comando reservar |
| Mientras popup otorga abierto | `p:poll` interval **59** → `actionTomarTurno` → SP | Timer Web ~59 s → `POST …/tomar` mientras dialog info turno visible |
| Choque entre operadores | SP: otro `session_id` y `fecha_session` < 3 min → error | Mismo mensaje en reservar / tomar / otorgar |
| Expiración | Job / consulta: `fecha_session` > 3 min → libera; sobreturno reservado > 30 min → DELETE | Al inicio de GET grilla + `POST …/expirar-reservas` |
| Identidad lock | `ts.general.f_get_session_id()` | **D-TUR-28:** `session_id` = id personal operador JWT (mismo usuario = misma sesión lógica) |

Estado del turno **no cambia** en tomar: sigue `RESERVADO`. Tomar no otorga.

#### C4 — tmp Oracle

Legacy llena `tmp_turno` / `tmp_turno_dia`. Migrado: el query **devuelve** filas; el comando reserva recibe `id_turno` (+ paciente/convenio/prestación). No crear `tmp_*` en PG.

#### C7 — Menú vs HOS-APP

| Path | Qué es | T5 |
|------|--------|----|
| `asignacionTurnos/agenda.xhtml` | Agenda HIS call center | **Canónica** |
| `pages/agenda/inicioAgenda.xhtml` | POST SSO a HOS-APP (`loginByPass`) | **Fuera** — no clonar |
| Web `pending('Turnero')` | Hoja a habilitar | → `/turnos/agenda` |

#### C8 — Gate UI (xhtml)

Paths:  
`Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/agenda.xhtml`  
`…/asignacionTurnos.xhtml` (west + popups)  
`…/infoTurno.xhtml` (Otorgar)  
Buscadores paciente/convenio/prestación/personal (reusar T2/T3 donde el popup coincida; **inventariar** si el de agenda difiere).

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Ver grilla día + calendario | `agenda.xhtml` · `f_get_grilla_dia` · `f_get_dias_disponibles` | **In scope** |
| Reservar slot | `actionBtnReservarTurno` · `f_reserva_turno_pac` | **In scope** |
| Otorgar | `BBAsignacionTurnos.actionBtnOtorgarTurno` · `f_otorga_turno_pac` | **In scope** |
| Tomar / lock sesión | unload `infoTurno` · `f_tomar_turno_pac` | **In scope** |
| Timeout reserva | `f_liberar_turno_reservado` | **In scope** (al consultar + comando; Quartz diferido) |
| Liberar | `actBtnLiberarTurno` · `f_libera_turno_pac` | **In scope** |
| Sobreturno | `actBtnSobreturno` · `f_reserva_sobreturno_pac` | **API done T5** · UI **hijo** [`turnos-agenda-sobreturno/`](../turnos-agenda-sobreturno/) Clarify **FIRME Camino 2** |
| Inhibición cruzada en reserva | `pp_inhibe_turno` | **In scope** |
| hist otorga/libera | `pf_hist_turno` | **In scope** |
| Equipo | mismos SP | **diferido** D-TUR-17 |
| Repetidos | `turnosRepetidos.xhtml` | **diferido** `turnos-agenda-repetidos` |
| Múltiples | `turnosMultiples.xhtml` | **diferido** `turnos-agenda-multiples` |
| Pre-agenda | `preAgendaTurnos.xhtml` | **diferido** `turnos-agenda-preagenda` |
| Ficha west paciente | tabs `asignacionTurnos.xhtml` | **diferido** `turnos-agenda-ficha-paciente` |
| Elegibilidad / cobros | ValidadorWS · popups pagar | **diferido** `turnos-agenda-elegibilidad-cobros` + P-ORA-010 |
| ABM tope paciente | `ctrlCtdMaxTurnosPac.xhtml` | **diferido** `turnos-ctrl-ctd-max-pac` |
| Reasignar menú fila agenda | `agenda.xhtml` L279 · `actBtnReemplazarTurno` | **hijo** [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) (Clarify **FIRME** 2026-09-09) |
| Cola `turnosAReasignar.xhtml` | `BBTurnosAReasignar` | **diferido T6** `turnos-ciclo-vida` |
| Consulta Agenda Turnero | `consulta.xhtml` · `BBConsultaAgenda` | **hijo T5.5** [`turnos-agenda-consultas/`](../turnos-agenda-consultas/) |
| Consultas menú | `consultaTurnos*.xhtml` | **diferido** [`turnos-consultas-operador/`](../turnos-consultas-operador/) (no mezclar con T5.5) |
| Imprimir turno BIRT | `f_imprime_turno_pac` | **diferido T7** |
| SMS/mail al otorgar | no en SP reserva/otorga | **N/A** (legacy no encola ahí) |
| Puente HOS-APP | `inicioAgenda.xhtml` | **WAIVE** con evidencia: es otra app, no HIS |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Query **grilla día** serv+pers: filtros equivalentes al SP (fecha, horas, paciente, conv/plan, prest, centro/serv/pers, filtro estado, call center) |
| RF-2 | Query **días disponibles** (calendario mes) |
| RF-3 | Comando **reservar**: `LIBRE`→`RESERVADO`; paciente/convenio/prestación; split/inhibe según golden |
| RF-4 | Comando **otorgar**: `RESERVADO`→`OTORGADO`; `hist_turno`; medio `PRESENCIAL`/`TELEFONICO` |
| RF-5 | Comando **tomar**: renueva lock `session_id`/`fecha_session` sobre `RESERVADO`; rechaza otro operador < 3 min |
| RF-5b | UI poll **59 s** en dialog info turno mientras otorga pendiente (paridad `p:poll`) |
| RF-6 | Comando **liberar**: vuelve `LIBRE` (DELETE si sobreturno); motivo; hist si no era solo reserva |
| RF-7 | Comando **sobreturno**: INSERT turno `sobreturno='S'` + flujo otorga |
| RF-8 | **Expirar reservas** al consultar grilla + `POST …/expirar-reservas`: 3 min / 30 min sobreturno |
| RF-9 | UI `/turnos/agenda` + dialogs + inventarios copy/validaciones + geometría xhtml |
| RF-10 | Gate call center + hab vigente (mensajes legacy) |
| NFR-1 | Capas starter; `/api/v1/turnos/agenda/...` |
| NFR-2 | Golden transiciones + lock (tomar, choque 3 min, expiración 3/30 min) |
| NFR-3 | Canon DDL `ts`; sin `tmp_turno*`; sin `*_agi` |
| NFR-4 | Paridad UI v1.10; toast; modal libera; error API sin `/500` |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-21 | T5 = este slug (`turnos-agenda-otorgar`) |
| D-TUR-22 | UI canónica = HIS `agenda.xhtml`; HOS-APP puente **fuera** |
| D-TUR-23 | Repetidos / múltiples / pre-agenda → hijos |
| D-TUR-24 | Ficha west paciente → `turnos-agenda-ficha-paciente` |
| D-TUR-25 | Elegibilidad WS + cobros → `turnos-agenda-elegibilidad-cobros` + P-ORA-010 |
| D-TUR-26 | Reasignar **menú fila agenda** → hijo [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) (Clarify **FIRME** 2026-09-09). Liberar simple queda en T5. Cola / suspender / reemplazo / vencidos / hist → `turnos-ciclo-vida`. |
| D-TUR-27 | Job Quartz `f_liberar_turno_reservado` **diferido**; v1 expira al consultar grilla + POST ops |
| D-TUR-28 | **Lock sesión opción A (FIRME):** tomar + poll 59 s + chequeo 3 min + expiración 3/30 min; `session_id` = operador JWT |
| D-TUR-17 | Equipo (sigue) |

## No objetivos

| Ítem | Destino |
|------|---------|
| Generar/eliminar grilla | T4 (hecho) |
| Suspender / reemplazo profesional / cola reasignar | T6 (`turnos-ciclo-vida`) |
| Reasignar menú fila agenda | [`turnos-agenda-reasignar/`](../turnos-agenda-reasignar/) |
| BIRT turno / SMS / APIs Apross | T7 |
| ABM catálogos / equipo | slugs existentes |
| Clonar HOS-APP / turnos online | fuera de Hospital-Web |

## Evidencia legacy (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/agenda.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/asignacionTurnos.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/infoTurno.xhtml
Hospital-Legacy/HOSPITAL_2/src/.../BBAgenda.java
Hospital-Legacy/HOSPITAL_2/src/.../BBAsignacionTurnos.java
Hospital-Legacy/HOSPITAL_2/src/.../BBInicioTurnos.java
Packages/Turnos/TURNOS.PACKAGE.sql
Packages/Turnos/TURNOS.PACKAGE_BODY.sql
Hospital-Api/.../V31__ts_agi_maestros.sql (ts.turno)
Hospital-Api/.../V43__ts_turnos_generacion_grilla.sql (hist_turno)
```

Menú: [`dump-menu-aplicacion.csv`](../../../relevamiento/relevamiento-his-orientacion/dump-menu-aplicacion.csv) — `ATENCION_TURNOS` 15000 · `turnero` 15001.  
Hoja 15003 `turnero_simple` = puente HOS-APP (D-TUR-22).
