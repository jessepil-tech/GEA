# Matriz Turnos — legacy ↔ destino

`phase_id:` **`sdd.hospital.relevamiento-turnos.matriz`**  
Fecha: **2026-09-09**  
Padre: [`README.md`](README.md)

Estados: **Migrado** | **Parcial** | **Diferido(`slug`)** | **No migrado** | **WAIVE**.

---

## 1. Persistencia

| Concepto | Legacy | Destino | Estado |
|----------|--------|---------|--------|
| Slot de agenda | Oracle `TS.TURNO` | PG `ts.turno` (V31, paridad columnas) | **DDL migrado**; oferta T4 `LIBRE`; otorgar/reserva/libera **T5 gate-done** 2026-09-07 |
| Habilitación | `HAB_TURNOS_*` | Flyway V36/V37 `hab_turnos_*` | **Parcial / gate-done T2** — serv+pers + check; equipo **gate-done** D-TUR-12 |
| Horarios / grupos | `HORARIO_TUR_GRP_*` / `GRP_PREST_TUR_*` | [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) | **gate-done** T3 2026-09-01 |
| Personal / call center | `PERSONAL`, `CALL_CENTER` | Flyway T1 [`turnos-maestros-personal/`](../../cortes/turnos/turnos-maestros-personal/) | **Parcial / gate-done DDL+API+UI** (ABM catálogo diferido) |
| Hist / vencidos / mensajes | `HIST_TURNO`, `TURNO_VENCIDO`, `MENSAJE_TURNO` | T6.3 hist **gate-done**; vencidos T6 **gate-done**; mensajes T7 [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) **gate-done** Camino 1 | **Parcial** (SMTP/ticket PDF hijos) |

`ts.turno` en V31 incluye FKs lógicas a personal, grupos, motivos, call center **diferidas** — ver [`pendientes-solo-oracle.md`](../../estado/pendientes-solo-oracle.md).

---

## 2. Package `TS.TURNOS` → Quarkus

| Función (muestra) | Destino | Estado |
|-------------------|---------|--------|
| `f_hab_turnos_*` / `f_check_hab_turnos` | CQRS `TurnosHab*` + `POST …/hab/check` (solo `hab_*.vigente`) | **Parcial / gate-done T2** — sync `atiende_turnos` diferido D-TUR-11 |
| `f_gen_grilla_turnos` / `f_elim_grilla_turnos` | [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) | **gate-done** T4 2026-09-04 |
| `f_consulta_agendas_generadas` + imprimir BIRT | [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) + hijo imprimir | **gate-done** T4 2026-09-04 |
| `f_get_grilla_dia` | [`turnos-agenda-otorgar/`](../../cortes/turnos/turnos-agenda-otorgar/) | **gate-done** T5 2026-09-07 |
| `f_reserva_*` / `f_otorga_turno_pac` / `f_libera_*` / `f_tomar_*` | [`turnos-agenda-otorgar/`](../../cortes/turnos/turnos-agenda-otorgar/) | **gate-done** T5 2026-09-07 |
| `f_reservar_turnos_repetidos` | [`turnos-agenda-repetidos/`](../../cortes/turnos/turnos-agenda-repetidos/) | **gate-done** T5.3 2026-09-11 |
| `f_suspender_*` / `f_reemplazar_personal` | T6.4 [`turnos-grilla-suspender/`](../../cortes/turnos/turnos-grilla-suspender/) **gate-done**; T6.5 [`turnos-grilla-reemplazo/`](../../cortes/turnos/turnos-grilla-reemplazo/) **gate-done** | **Cerrado** |
| Reasignar menú agenda (`otorgarTurnoPac` + origen) | [`turnos-agenda-reasignar/`](../../cortes/turnos/turnos-agenda-reasignar/) | **gate-done** T6.1 2026-09-10 — overlay UI; cierre reusa T5 |
| `f_migra_turno_vencido` | T6 [`turnos-ciclo-vida/`](../../cortes/turnos/turnos-ciclo-vida/) **gate-done** (archivo + cola 6 h; safety 1 h ya cobrada) | **Cerrado** |
| `f_imprime_turno_pac` | Reports sidecar `Turno` | **gate-done** cable T5 [`turnos-agenda-imprimir-turno/`](../../cortes/turnos/turnos-agenda-imprimir-turno/); SP coseguro **diferido** |
| Lectura AGI OTORGADO | `JdbcAgiAdapter` / `ListTurnosQuery` | **Migrado** (consumo) |
| Marca RECEPCIONADO | AGI confirmar | **Migrado** (otro módulo) |

Lógica de negocio del package **no** se clona como PL/pgSQL: se porta a handlers CQRS sobre `ts.*` (regla de programa).

---

## 3. UI

| Legacy | Hospital-Web | Estado |
|--------|--------------|--------|
| Módulo `ATENCION_TURNOS` (árbol completo) | Tile TURNOS disabled | **Parcial** shell |
| `asignacionTurnos/agenda.xhtml` | `/turnos/agenda` | **Parcial** — T5–T5.3-b + T6.1 **gate-done**; T6 vencidos **gate-done** (sin hoja); P-ORA-010 abierto |
| `atencionTurno/generacionGrillaTurnos.xhtml` | `/configuracion/grilla-turnos-generar` · [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) | **gate-done** T4 2026-09-04 |
| `atencionTurno/eliminarGrillaTurnos.xhtml` | `/configuracion/grilla-turnos-eliminar` | **gate-done** T4 2026-09-04 |
| `atencionTurno/consultaAgendasGeneradas.xhtml` | `/configuracion/grilla-turnos-consulta` + hijos drill-down/imprimir | **gate-done** T4 2026-09-04 |
| Config `habTurnosServCentro` / `habTurnosPersServ` | `/configuracion/hab-turnos-serv` · `…-pers` | **Parcial / gate-done T2** — buscadores → [`turnos-hab-buscadores/`](../../cortes/turnos/turnos-hab-buscadores/) |
| Config `habTurnosEquipoServ` | `/configuracion/hab-turnos-equipo` · [`turnos-hab-equipo/`](../../cortes/turnos/turnos-hab-equipo/) | **gate-done** 2026-09-23 D-TUR-12 |
| Config `turnosEquipo` | `/configuracion/horarios-turnos-equipo` · [`turnos-horarios-equipo/`](../../cortes/turnos/turnos-horarios-equipo/) | **gate-done** 2026-09-23 D-TUR-13. Especial, inhibición, ocupación y generar grilla afuera |
| Tótem lista turnos del paciente | `/agi/recepcion` | **Migrado** (no es paridad del módulo TURNOS) |

---

## 4. Jobs

| Legacy | Destino | Estado |
|--------|---------|--------|
| `CheckHabTurnosJob` | `POST /api/v1/turnos/hab/check` (ops; Quartz opcional) | **Parcial / gate-done T2** (comando API; job scheduler no) |
| `MigrarTurnoVencidoJob` | T6 [`turnos-ciclo-vida/`](../../cortes/turnos/turnos-ciclo-vida/) **gate-done** | **Cerrado** |
| MailTurnoJob | T7 [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) **gate-done** Camino 1 (sin SMTP) | **Cerrado** |
