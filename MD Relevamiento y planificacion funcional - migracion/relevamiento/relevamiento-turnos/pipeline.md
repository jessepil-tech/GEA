# Pipeline — Turnos / agenda / grilla

`phase_id:` **`sdd.hospital.relevamiento-turnos.pipeline`**  
Fecha: **2026-09-09**  
Padre: [`README.md`](README.md) · Proceso: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md)

Guion **configuración → materialización → operación**. Ninguna etapa en silencio.

---

## Diagrama

```text
Personal / Identity
    → Permiso menú ATENCION_TURNOS + call center (PERSONAL_CALL_CENTER)
    → Maestros: centro / servicio / prestación / convenio / personal médico
    → Habilitación HAB_TURNOS_{SERV_CENTRO | PERS_SERV | EQUIPO_SERV}
    → Grupos + franjas (GRP_PREST_TUR_* + HORARIO_TUR_GRP_* + DIA_HORARIO_*)
    → Generación: TS.TURNOS.f_gen_grilla_turnos → filas ts.turno (LIBRE)
         ↓
Operación: agenda / reservar / otorgar / consultar
         ↓
Ciclo: suspender / liberar / reasignar / vencidos
         ↓
Side-effects: MENSAJE_TURNO, BIRT turno, cola recepción, anunciador (post-llamar)
```

**Anti-sesgo:** la grilla del día es A7, no el módulo.

---

## Fases A1–A9

| # | Etapa | Legacy (evidencia) | Destino | Estado |
|---|-------|--------------------|---------|--------|
| A1 | Identidad / personal | Operador: `PERSONAL_CALL_CENTER` (`BBInicioTurnos` · `inicioTurnos.xhtml`); médico/equipo en `PERSONAL`; admin servicio: `PERSONAL_ADM_TUR_SERV_CENTRO` | JWT Identity; **sin** `ts.personal` / `ts.call_center` en Flyway Api | **No migrado** |
| A2 | Roles / perfiles | Módulo menú `ATENCION_TURNOS`; árbol por perfil | Tile `menuKey: ATENCION_TURNOS` con hoja `pending()` → **disabled** | **Shell only** |
| A3 | Maestros | Centro, servicio/centro, prestación, convenio/plan, personal médico | V28 `centro_atencion`; V30 `servicio`; V31 paciente/convenio/plan/prestacion/`turno` | **DDL parcial**; ABM **diferido**; datos seed/IT |
| A4 | Habilitación | `HAB_TURNOS_*`; job `CheckHabTurnosJob` → `TS.TURNOS.f_check_hab_turnos` | Flyway V36/V37 + API/UI T2 | **Parcial / gate-done T2** — sync `atiende_turnos` diferido D-TUR-11 |
| A5 | Horarios / grupos | `GRP_PREST_TUR_*` + `PREST_GRP_*`; `HORARIO_TUR_GRP_*` + `DIA_HORARIO_*`; inhibiciones | [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) **gate-done** 2026-09-01 | **gate-done** 2026-09-01 — inhibiciones diferidas hijo |
| A6 | Generación | `BBGeneracionGrillaTurnos` → `f_gen_grilla_turnos` (slots `LIBRE`); `f_elim_grilla_turnos`; `f_consulta_agendas_generadas` | [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) **gate-done** 2026-09-04 | **gate-done** 2026-09-04 — equipo D-TUR-17; horario especial D-TUR-15/20; SMS T7 |
| A7 | Operación diaria | `asignacionTurnos/agenda.xhtml` (`BBAgenda`); `f_get_grilla_dia`; `f_reserva_*`; `f_otorga_turno_pac`; `f_libera_*`; `f_tomar_*`; `f_reserva_sobreturno_pac` | T5 + T5.1–T5.1e **gate-done**; persist **gate-done**; T5.2 **gate-done**; T6.1 **gate-done**; AGI consume `OTORGADO` | **Parcial** — prep diferido; WS OS P-ORA-010 |
| A8 | Ciclo de vida | `LIBRE`→`RESERVADO`→`OTORGADO`→`RECEPCIONADO`; + suspender/inhibir/reasignar; `f_migra_turno_vencido` | Hijo [`turnos-agenda-reasignar/`](../../cortes/turnos/turnos-agenda-reasignar/) **gate-done**. Cola [`turnos-agenda-cola-reasignar/`](../../cortes/turnos/turnos-agenda-cola-reasignar/) **gate-done**. Hist [`turnos-agenda-historial/`](../../cortes/turnos/turnos-agenda-historial/) **gate-done**. Suspender [`turnos-grilla-suspender/`](../../cortes/turnos/turnos-grilla-suspender/) **gate-done**. Reemplazo [`turnos-grilla-reemplazo/`](../../cortes/turnos/turnos-grilla-reemplazo/) **gate-done**. AGI marca `RECEPCIONADO`. Vencidos [`turnos-ciclo-vida/`](../../cortes/turnos/turnos-ciclo-vida/) **gate-done** | **Cerrado** — T6.1–T6.5 + job vencidos; T7 avisos fuera |
| A9 | Side-effects | `MENSAJE_TURNO` + `MailTurnoJob`; ticket PDF turno; cola/anunciador vía recepción | Ticket/cola/TV del piloto recepción. Avisos [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) **gate-done**. Ticket PDF Agenda [`turnos-agenda-imprimir-turno/`](../../cortes/turnos/turnos-agenda-imprimir-turno/) **gate-done** | **Parcial** — avisos + ticket Agenda cobrados; APIs / SMTP **diferido** |

---

## PASS / gaps de esta fase

| Criterio | Resultado |
|----------|-----------|
| A1–A9 inventariadas | **Sí** |
| A1–A5 sin tratar seed AGI como “cerrado” | **Sí** — ver [`maestros.md`](maestros.md) |
| Operación A7 como única evidencia de paridad | **Prohibido** — sin A4–A6 no hay oferta regenerable |
