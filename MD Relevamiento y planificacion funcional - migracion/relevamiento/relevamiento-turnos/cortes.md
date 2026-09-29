# Cortes propuestos — Turnos

`phase_id:` **`sdd.hospital.relevamiento-turnos.cortes`**  
Fecha: **2026-09-14**  
Solo después de pipeline + maestros.

Orden por **dependencia**. No empezar por la grilla del día.

| Orden | Corte | Qué incluye | Prerrequisito | Slug / estado |
|-------|-------|-------------|---------------|---------------|
| T0 | Relevamiento capa 3 | Esta carpeta | — | **Hecho** 2026-08-27 |
| T1 | Maestros identidad turno | DDL `ts.personal` (+ vínculos); `call_center` / `personal_call_center`; motivos; permiso menú (picker) | Identity oleada A | **Gate parcial** [`turnos-maestros-personal/`](../../cortes/turnos/turnos-maestros-personal/) |
| T2 | Habilitación | DDL+ABM o sync `hab_turnos_*`; paridad `f_check_hab_turnos` | T1 + centro/servicio | **gate-done** [`turnos-config-hab-horarios/`](../../cortes/turnos/turnos-config-hab-horarios/) (2026-08-27) |
| T3 | Grupos + horarios | `grp_prest_tur_*`, prestaciones del grupo, `horario_tur_grp_*`, días; inhibiciones → hijo | T2 | **gate-done** [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) (2026-09-01) |
| T4 | Generación grilla | Port `f_gen_grilla_turnos` / `f_elim_grilla_turnos` + consulta agendas; CQRS sobre `ts.turno` | T3 | **gate-done** [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) (2026-09-04) |
| T5 | Operación agenda | Reservar / otorgar / liberar / sobreturno; UI módulo TURNOS | T4 | **gate-done** [`turnos-agenda-otorgar/`](../../cortes/turnos/turnos-agenda-otorgar/) (2026-09-07) |
| T5.1 | Ficha paciente + west agenda | Buscadores north paciente/convenio; west ficha / Desde-Hasta / otros centros | T5 | **gate-done** [`turnos-agenda-ficha-paciente/`](../../cortes/turnos/turnos-agenda-ficha-paciente/) (2026-09-08) |
| T5.1b | Info popups north | Help búsqueda + info convenio (chrome doc req) | T5.1 | **gate-done** 2026-09-10 [`turnos-agenda-info-popups/`](../../cortes/turnos/turnos-agenda-info-popups/) |
| T5.1c | Elegibilidad north + doc req | Afiliado/icono/seed; filas `ts.doc_req_*` | T5.1b | **gate-done** 2026-09-10 [`turnos-agenda-elegibilidad-cobros/`](../../cortes/turnos/turnos-agenda-elegibilidad-cobros/) |
| T5.1d | Cobros agenda | Rechazo → convenio dflt + popup obs; pagar/saldo/coseguro display | T5.1c | **gate-done** 2026-09-10 [`turnos-agenda-cobros/`](../../cortes/turnos/turnos-agenda-cobros/) |
| T5.1e | Chrome infoTurno | Dialog 1200×520 un-turno; ficha + doc-req al abrir | T5 | **gate-done** 2026-09-10 [`turnos-agenda-info-turno/`](../../cortes/turnos/turnos-agenda-info-turno/) |
| T5.1e-p | Persist obs/fecha prescripción | UPDATE al otorgar + lectura Información + validaciones HIS | T5.1e | **gate-done** 2026-09-10 [`turnos-agenda-info-turno-persist/`](../../cortes/turnos/turnos-agenda-info-turno-persist/) |
| T5.1e-q | Prep / requisitos realización | GET al abrir infoTurno un-turno + G1 DDL/seed CONS | T5.1e | **gate-done** 2026-09-10 [`turnos-agenda-info-turno-prest/`](../../cortes/turnos/turnos-agenda-info-turno-prest/) |
| T5.2 | Sobreturno agenda | Accordion 170 + popup `$popupSobreturno`; INSERT `sobreturno='S'` + otorga T5 | T5 | **gate-done** 2026-09-10 [`turnos-agenda-sobreturno/`](../../cortes/turnos/turnos-agenda-sobreturno/) |
| T5.3 | Turnos repetidos | Gear TURNOS REPETIDOS → popup días/ctd → reserva N → tablas → otorga o Volver | T5 | **gate-done** 2026-09-11 [`turnos-agenda-repetidos/`](../../cortes/turnos/turnos-agenda-repetidos/) |
| T5.3-b | Cambiar horario repetidos | `$popupCambiarTurno` 1200×550 · LIBRES del día · reemplaza fila / completa obs | T5.3 | **gate-done** 2026-09-11 [`turnos-agenda-repetidos-cambiar/`](../../cortes/turnos/turnos-agenda-repetidos-cambiar/) |
| T5.4 | Turnos múltiples | Turnero → carrito N prestaciones mismo día → consultar → reserva lote → infoTurno | T5 | **gate-done** [`turnos-agenda-multiples/`](../../cortes/turnos/turnos-agenda-multiples/) (2026-09-14) |
| T5.4-b | Cambiar horario múltiples | `$popupCambiarTurno` 1200×550 · LIBRES del día · swap in-memory | T5.4 | **gate-done** 2026-09-14 [`turnos-agenda-multiples-cambiar/`](../../cortes/turnos/turnos-agenda-multiples-cambiar/) |
| T5.5 | Consulta Agenda | Hoja Turnero `consulta.xhtml`: north rango + tabla `ts.turno`; Imprimir cable sidecar; Excel + PDF valores → hijos | T5 | **gate-done** 2026-09-15 [`turnos-agenda-consultas/`](../../cortes/turnos/turnos-agenda-consultas/) |
| T5.5-pdf | PDF Consulta Agenda | Contenido sidecar `ConsultaAgenda` = grilla hoy; Flyway `turno_vencido` + `equipo_serv_centro` vacías + params Todos | T5.5 | **gate-done** 2026-09-15 [`turnos-agenda-consultas-pdf/`](../../cortes/turnos/turnos-agenda-consultas-pdf/) |
| T5.5-excel | Excel Consulta Agenda | POI HSSF `.xls` south Turnero; `ExcelExportPort`; no sidecar | T5.5 | **gate-done** 2026-09-16 [`turnos-agenda-consultas-export/`](../../cortes/turnos/turnos-agenda-consultas-export/) |
| T5.6 | Pre-agenda Turnero | Hoja `preAgendaTurnos.xhtml`: lista PENDIENTE + Asignar → Agenda | T5 | **gate-done** 2026-09-15 [`turnos-agenda-preagenda/`](../../cortes/turnos/turnos-agenda-preagenda/) |
| T6 | Ciclo de vida (vencidos) | `f_migra_turno_vencido` · job (archivo + cola 6 h) | T5 | **gate-done** 2026-09-21 D-TUR-75 [`turnos-ciclo-vida/`](../../cortes/turnos/turnos-ciclo-vida/) |
| T6.4 | Suspender / quitar grilla | `suspenderGrillaTurnos` + `quitarCancelacionGrillaTurnos`; port `f_suspender_turnos` / `f_quitar_suspension` | T4 | **gate-done** 2026-09-18 [`turnos-grilla-suspender/`](../../cortes/turnos/turnos-grilla-suspender/) |
| T6.5 | Reemplazo profesional grilla | `reemplazoPersonalGrillaTurnos`; port `f_reemplazar_personal` (reemplazar + quitar) | T4 | **gate-done** D-TUR-74 [`turnos-grilla-reemplazo/`](../../cortes/turnos/turnos-grilla-reemplazo/) |
| T6.1 | Reasignar desde agenda | Menú fila REASIGNAR / CANCELAR_REASIGNACION; overlay `PENDIENTE_LIBERAR`; cierre otorga+libera origen | T5 D-TUR-26 | **gate-done** 2026-09-10 [`turnos-agenda-reasignar/`](../../cortes/turnos/turnos-agenda-reasignar/) |
| T6.2 | Cola Reasignación de Turnos | Accordion `turnosAReasignar.xhtml`; lista `ts.turno_a_reasignar`; IrAGrilla reusa T6.1 | T4 + T6.1 | **gate-done** 2026-09-17 [`turnos-agenda-cola-reasignar/`](../../cortes/turnos/turnos-agenda-cola-reasignar/) |
| T6.2-print | PDF cola Reasignación | South Imprimir → sidecar `TurnosAReasignar`; mismos filtros que Consultar | T6.2 | **gate-done** 2026-09-17 [`turnos-agenda-cola-reasignar-print/`](../../cortes/turnos/turnos-agenda-cola-reasignar-print/) |
| T6.2-excel | Excel cola Reasignación | South Exportar Excel → POI HSSF; mismos filtros que Consultar | T6.2 | **gate-done** 2026-09-17 [`turnos-agenda-cola-reasignar-export/`](../../cortes/turnos/turnos-agenda-cola-reasignar-export/) |
| T6.3 | Historial Turnos | Accordion `historialTurno.xhtml`; lista `ts.hist_turno`; info popup | T5 | **gate-done** 2026-09-18 [`turnos-agenda-historial/`](../../cortes/turnos/turnos-agenda-historial/) |
| T6.3-excel | Excel Historial | South Exportar Excel → POI HSSF | T6.3 | **gate-done** 2026-09-18 [`turnos-agenda-historial-export/`](../../cortes/turnos/turnos-agenda-historial-export/) |
| T7 | Side-effects (avisos) | `mensaje_turno` + `MailTurnoJob` (Camino 1: REPROGRAMACION + despacho sin SMTP si no hay server). Ticket PDF turno y APIs externas → hijos. | T5–T6 | **gate-done** 2026-09-22 D-TUR-76 [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) |

## Qué no hacer primero

- Pantalla “grilla del día” sin T1–T4 (perpetúa seed de OTORGADO).
- ABM de una fila `turno` suelta como si fuera el módulo.
- Ensanchar AGI para “crear turnos demo” en lugar de generar oferta.

## Primer CU de implementación

T1 [`turnos-maestros-personal/`](../../cortes/turnos/turnos-maestros-personal/) — gate parcial.  
T2 [`turnos-config-hab-horarios/`](../../cortes/turnos/turnos-config-hab-horarios/) — **gate-done** 2026-08-27.  
T3 [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) — **gate-done** 2026-09-01.  
T4 [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) — **gate-done** 2026-09-04.  
**Siguiente:** las hermanas de Equipo quedaron en su slug el 2026-09-28 (consulta, cola, sobreturno, múltiples, suspender/quitar, historial). [`turnos-agenda-historial-equipo/`](../../cortes/turnos/turnos-agenda-historial-equipo/) **gate-done** 2026-09-28. [`turnos-consultas-preagenda/`](../../cortes/turnos/turnos-consultas-preagenda/) queda propuesto, aparte del flujo Equipo. D-TUR-17 tramo 2 [`turnos-agenda-equipo/`](../../cortes/turnos/turnos-agenda-equipo/) **gate-done** 2026-09-28. Tramo 1 [`turnos-grilla-equipo/`](../../cortes/turnos/turnos-grilla-equipo/) **gate-done** 2026-09-24. D-TUR-13 [`turnos-horarios-equipo/`](../../cortes/turnos/turnos-horarios-equipo/) **gate-done** 2026-09-23. Hab equipo [`turnos-hab-equipo/`](../../cortes/turnos/turnos-hab-equipo/) **gate-done** 2026-09-23 (D-TUR-12). Cola corta: menú `consultaPreagenda`; alta ATENCION. Coseguro del ticket (`f_imprime_turno_pac`) y ficha oeste Imprimir = hijos. No clonar HOS-APP.

## Relación con AGI / mostrador

AGI **consume** `ts.turno` OTORGADO y marca `RECEPCIONADO`. Eso **no** cierra TURNOS.  
Hasta T5, un `OTORGADO` copiado de Oracle **solo** prueba lectura/cola/llamar
y debe etiquetarse bootstrap — **no** cierra otorgar.  
Cutover de **escritura de oferta** (T4–T5) es permanente: [`criterio-avance-e2e-datos.md`](../../canon/criterio-avance-e2e-datos.md).

## Ready de implementación

T0 cubierto. T1 gate parcial. **T2–T7 + T5.5-excel + T6.1 + T6.2 + T6.2-print + T6.2-excel + T6.3 + T6.3-excel + T6.4 + T6.5 + T5-print gate-done**.  
Cola corta: hermanas de Equipo diferidas, sin abrir. [`turnos-agenda-historial-equipo/`](../../cortes/turnos/turnos-agenda-historial-equipo/) **gate-done** 2026-09-28; alta ATENCION. [`turnos-consultas-preagenda/`](../../cortes/turnos/turnos-consultas-preagenda/) propuesto, aparte. D-TUR-17 tramo 2 [`turnos-agenda-equipo/`](../../cortes/turnos/turnos-agenda-equipo/) **gate-done** 2026-09-28; tramo 1 grilla **gate-done** 2026-09-24. Hab equipo **gate-done**. D-TUR-13 [`turnos-horarios-equipo/`](../../cortes/turnos/turnos-horarios-equipo/) **gate-done** 2026-09-23. No clonar HOS-APP.
