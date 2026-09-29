# Maestros y seguridad — Turnos

`phase_id:` **`sdd.hospital.relevamiento-turnos.maestros`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md)

Tabla anti-omisión (Fase C). **Seed de `ts.turno` OTORGADO ≠ paridad de configuración.**

---

## Inventario

| Maestro / permiso | ¿Bloquea operación si falta? | ¿Existe CU/SDD? | Estado migración |
|-------------------|------------------------------|-----------------|------------------|
| Usuario + perfil menú `ATENCION_TURNOS` | Sí (no entra al módulo) | Shell Web | **Parcial** — tile disabled |
| `ts.call_center` + `personal_call_center` | Sí (gate `BBInicioTurnos`) | V34+V35 + API/UI T1 | **Gate migrado**; ABM diferido |
| `ts.personal` (médico / otorga / suspende) | Sí para generar/otorgar | V34+V35; FK turno diferida | **DDL + lookup login**; ABM diferido |
| `personal_adm_tur_serv_centro` | Sí para quien administra turnos del servicio | V34 DDL | **DDL**; UI/ABM diferido T2 |
| `ts.centro_atencion` | Sí | V28 | **DDL**; ABM diferido |
| `ts.servicio` (+ vínculo centro) | Sí | V30 | **DDL**; ABM diferido |
| `ts.prestacion` | Sí al otorgar | V31 | **DDL**; ABM diferido |
| `ts.convenio` / `ts.plan_convenio` | Sí al otorgar | V31 · CU-A convenios (parcial) | **DDL**; ABM **parcial** |
| `ts.paciente` | Sí | V31 · AGI | **DDL + uso AGI** |
| `hab_turnos_serv_centro` / `_pers_serv` / `_equipo_serv` | Sí — sin hab no hay oferta | [`turnos-config-hab-horarios/`](../../cortes/turnos/turnos-config-hab-horarios/) T2 · [`turnos-hab-equipo/`](../../cortes/turnos/turnos-hab-equipo/) | **gate-done T2** serv+pers; equipo **gate-done** D-TUR-12 |
| `grp_prest_tur_*` + `prest_grp_prest_tur_*` | Sí — franjas y duración | [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) T3 | **gate-done** T3 2026-09-01 (pers+serv) |
| `horario_tur_grp_*` + `dia_horario_*` | Sí — input de `f_gen_grilla_turnos` | mismo T3 | **gate-done** T3 2026-09-01 |
| `horario_inh_tur_*` | No duro (reduce oferta) | hijo `turnos-horarios-inhibiciones` | **Diferido** (D-TUR-14) |
| `motivo` / `tipo_motivo_df` (suspende, sobreturno, reemplazo) | Sí para esas acciones | V34+V35 + GET | **Seed + GET**; ABM diferido |
| `ts.turno` (slots) | Sí — sin filas no hay agenda | V31 DDL + T4 CQRS + T5 ops | **DDL**; T4 generar/eliminar `LIBRE`; T5 reserva/otorga/libera |
| `ctrl_turnos_pac` | Condicional (tope por paciente) | ninguno | **No migrado** |
| `pre_agenda_turno` | No duro (canal) | [`turnos-agenda-preagenda/`](../../cortes/turnos/turnos-agenda-preagenda/) | **T5.6 gate-done** lista+Asignar; alta ATENCION fuera; V54 IF NOT EXISTS + V55 seed |
| `mensaje_turno*` + mail/SMS centro | No para otorgar; sí para notificación | [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) | **gate-done** T7 Camino 1 |

---

## Evidencia ABM / config legacy

| Capacidad | Legacy | Destino hoy |
|-----------|--------|-------------|
| Habilitación servicio/personal/equipo | `pages/configuracion/servicio/habTurnos*.xhtml` · `BBHabTurnos*` | **gate-done T2** UI serv+pers; equipo diferido D-TUR-12; buscadores `turnos-hab-buscadores` |
| Horarios / grupos | `pages/configuracion/servicio/turnos{Personal\|Servicios\|Equipo}/*` · `pages/atencionTurno/horarioGrp*.xhtml` | T3 **gate-done** 2026-09-01 (pers+serv; equipo diferido) |
| Quién administra | `personalAdministraTurno.xhtml` · `BBPersonalAdministraTurno` | Ausente |
| Tope paciente | `ctrlCtdMaxTurnosPac.xhtml` | Ausente |
| Generación / borrado grilla | `atencionTurno/generacionGrillaTurnos.xhtml` · `eliminarGrillaTurnos.xhtml` | **gate-done T4** 2026-09-04 — generar / eliminar / consulta (+ hijos drill-down / imprimir) |
| Consulta agendas generadas | `atencionTurno/consultaAgendasGeneradas.xhtml` | **gate-done T4** 2026-09-04 |
| Agenda / otorgar | `turnos/asignacionTurnos/agenda.xhtml` · `BBAgenda` | [`turnos-agenda-otorgar/`](../../cortes/turnos/turnos-agenda-otorgar/) **gate-done** 2026-09-07; ficha [`turnos-agenda-ficha-paciente/`](../../cortes/turnos/turnos-agenda-ficha-paciente/) **gate-done** 2026-09-08; AGI consume OTORGADO |

Package: `TS.TURNOS` (`Hospital-Legacy/RDBMS/.../Package Turnos.sql` · mapping `Turnos.hbm.xml`).  
DDL destino del **slot**: `Hospital-Api` `V31__ts_agi_maestros.sql` (`ts.turno`).  
Tablas de hab: **V36/V37** en Flyway Api (gate-done T2). Horario/grupo: SDD [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) **gate-done** 2026-09-01.

---

## Regla de cierre

A7 (agenda/otorgar) **gate-done** 2026-09-07. A4–A6 **gate-done** (T2–T4). T6 ciclo de vida **diferido**.
Cada fila: **done / diferido(slug) / WAIVE**.

Slug de deuda config: **`turnos-config-hab-horarios`** — **gate-done T2** [`../turnos-config-hab-horarios/`](../../cortes/turnos/turnos-config-hab-horarios/) (T3 horarios = [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) **gate-done**).  
Deuda sync oferta: **D-TUR-11** (`atiende_turnos` en tablas puente). Buscadores UI: **[`turnos-hab-buscadores/`](../../cortes/turnos/turnos-hab-buscadores/)** (**gate-done**).
