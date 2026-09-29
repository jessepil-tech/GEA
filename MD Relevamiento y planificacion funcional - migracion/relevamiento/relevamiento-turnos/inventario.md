# Inventario de capacidades — Turnos

`phase_id:` **`sdd.hospital.relevamiento-turnos.inventario`**  
Fecha: **2026-09-14**  
Padre: [`README.md`](README.md)

---

## Configuración / habilitación / generación

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después qué | Estado |
|-----------|------------------|---------------|------------|-------------|--------|
| Alta/vínculo personal call center | `PERSONAL_CALL_CENTER` · `BBInicioTurnos` | INSERT vínculo | No | Entra al módulo | **Diferido** T1 |
| Admin turnos por servicio | `BBPersonalAdministraTurno` | INSERT | No | Quién genera/configura | **Diferido** T1 |
| HAB turnos serv/pers/equipo | `BBHabTurnos*` · `f_hab_turnos_*` | INSERT/UPDATE | No | Horarios | **gate-done T2** serv+pers; equipo **gate-done** D-TUR-12 |
| Job vigencia hab | `CheckHabTurnosJob` · `f_check_hab_turnos` | UPDATE | No | Oferta válida | **gate-done T2** check `hab_*`; sync `atiende_turnos` diferido D-TUR-11 |
| Grupos prestación turno | config `turnosPersonal/Servicios/Equipo` | INSERT | No | Franjas | **T3 gate-done** [`turnos-horarios-grupos/`](../../cortes/turnos/turnos-horarios-grupos/) |
| Horarios / días / inhibiciones | `BBHorarioGrp*` · `BBInhTur*` | INSERT/UPDATE | No | Generación | **T3 gate-done** horario/días; inhibiciones diferido hijo |
| Generar grilla | `BBGeneracionGrillaTurnos` · `f_gen_grilla_turnos` | INSERT `turno` LIBRE | No | Agenda | **gate-done T4** 2026-09-04 [`turnos-generacion-grilla/`](../../cortes/turnos/turnos-generacion-grilla/) |
| Eliminar grilla | `BBEliminarGrillaTurno` · `f_elim_grilla_turnos` | DELETE | No | Regenerar | **gate-done T4** 2026-09-04 |
| Consulta agendas generadas | `BBConsultaAgendasGeneradas` | No | No | Operación | **gate-done T4** 2026-09-04 (G6 smoke + e2e) |
| Tope turnos paciente | `ctrlCtdMaxTurnosPac.xhtml` | INSERT/UPDATE | No | Otorgar | **Diferido** T3/T5 |
| Permiso menú `ATENCION_TURNOS` | Seguridad HOSPITAL_2 | N/A | No | Abre módulo | **Parcial** (tile disabled) |

## Operación diaria

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después qué | Estado |
|-----------|------------------|---------------|------------|-------------|--------|
| Ver grilla día | `agenda.xhtml` · `f_get_grilla_dia` | No | No | Reservar | **Active T5** |
| Reservar / sobreturno | `f_reserva_turno_pac` / `f_reserva_sobreturno_pac` | UPDATE/INSERT | No | Otorgar | Reserva **gate-done T5**; UI sobreturno [`turnos-agenda-sobreturno/`](../../cortes/turnos/turnos-agenda-sobreturno/) Clarify **FIRME Camino 2** |
| Otorgar | `f_otorga_turno_pac` | UPDATE `OTORGADO` | No | Recepción AGI | **Active T5** |
| Prep / req realización en infoTurno | `infoTurno.xhtml` · `selectPreparacionPrestEdad` · `selectReqRealizaPrestEqual` | No (GET) | No | Otorgar / informar | **gate-done** 2026-09-10 [`turnos-agenda-info-turno-prest/`](../../cortes/turnos/turnos-agenda-info-turno-prest/) |
| Liberar / tomar | `f_libera_turno_pac` · `f_tomar_turno_pac` | UPDATE | No | Oferta | **Active T5** |
| Turnos repetidos | `turnosRepetidos.xhtml` · `f_reservar_turnos_repetidos` | Sí | No | Otorgar | **gate-done T5.3** [`turnos-agenda-repetidos/`](../../cortes/turnos/turnos-agenda-repetidos/) |
| Múltiples / pre-agenda | xhtml asignacionTurnos + `f_reserva_*_multiple*` | Sí | No | Otorgar | **T5.4 gate-done** [`turnos-agenda-multiples/`](../../cortes/turnos/turnos-agenda-multiples/); cambiar horario **gate-done** [`turnos-agenda-multiples-cambiar/`](../../cortes/turnos/turnos-agenda-multiples-cambiar/); pre-agenda **T5.6 gate-done** [`turnos-agenda-preagenda/`](../../cortes/turnos/turnos-agenda-preagenda/) |
| Consulta Agenda (Turnero) | `consulta.xhtml` · `BBConsultaAgenda` | No | No | Info turno T5 | **gate-done T5.5** [`turnos-agenda-consultas/`](../../cortes/turnos/turnos-agenda-consultas/) (Imprimir cable; Excel + PDF valores diferidos) |
| Consultas operador / historial | `consultaTurnos*.xhtml` | No | No | — | **Diferido** [`turnos-consultas-operador/`](../../cortes/turnos/turnos-consultas-operador/) (no mezclar con T5.5) |
| Listar turnos OTORGADO (tótem) | AGI (no es el módulo TURNOS) | No | No | Confirmar recepción | **Migrado** (consumo) |
| Confirmar recepción | AGI G1 | UPDATE `RECEPCIONADO` | No | Cola / ticket | **Migrado** (otro módulo) |

## Ciclo de vida / excepciones

| Capacidad | Evidencia | Estado |
|-----------|-----------|--------|
| Suspender / quitar suspensión | `BBSuspenderGrillaTurnos` · `f_suspender_turnos` | **gate-done** T6.4 [`turnos-grilla-suspender/`](../../cortes/turnos/turnos-grilla-suspender/) |
| Reemplazo personal en grilla | `BBReemplazoPersonalGrillaTurnos` · `f_reemplazar_personal` | **gate-done** T6.5 [`turnos-grilla-reemplazo/`](../../cortes/turnos/turnos-grilla-reemplazo/) |
| Reasignar (menú fila agenda) | `BBAgenda.actBtnReemplazarTurno` · overlay `PENDIENTE_LIBERAR` | **gate-done** T6.1 2026-09-10 [`turnos-agenda-reasignar/`](../../cortes/turnos/turnos-agenda-reasignar/) |
| Cola turnos a reasignar | `BBTurnosAReasignar` · `turnosAReasignar.xhtml` · `f_get_turnos_a_reasignar` | **gate-done** T6.2 [`turnos-agenda-cola-reasignar/`](../../cortes/turnos/turnos-agenda-cola-reasignar/); Imprimir **gate-done** [`turnos-agenda-cola-reasignar-print/`](../../cortes/turnos/turnos-agenda-cola-reasignar-print/); Excel **gate-done** [`turnos-agenda-cola-reasignar-export/`](../../cortes/turnos/turnos-agenda-cola-reasignar-export/) |
| Vencidos | `f_migra_turno_vencido` · `MigrarTurnoVencidoJob` | **gate-done** T6 [`turnos-ciclo-vida/`](../../cortes/turnos/turnos-ciclo-vida/) |
| Historial `hist_turno` | `BBHistorialTurno` · `historialTurno.xhtml` | **gate-done** T6.3 [`turnos-agenda-historial/`](../../cortes/turnos/turnos-agenda-historial/); Excel **gate-done** [`turnos-agenda-historial-export/`](../../cortes/turnos/turnos-agenda-historial-export/) |

## Side-effects

| Capacidad | Evidencia | Estado |
|-----------|-----------|--------|
| Mail/SMS cola `mensaje_turno` | `MailTurnoJob` | T7 [`turnos-avisos/`](../../cortes/turnos/turnos-avisos/) **gate-done** Camino 1 |
| Imprimir turno (sidecar) | `f_imprime_turno_pac` + `Turno.rptdesign` | **gate-done** T5 [`turnos-agenda-imprimir-turno/`](../../cortes/turnos/turnos-agenda-imprimir-turno/) Camino 1; SP coseguro diferido |
| Cola recepción / TV | Tras recepción + Llamar | **Migrado** (perímetro AGI/recepción) |
| APIs externas / Apross | `f_*_api` · `p_confirmar_turno_api` | **Diferido** hijo de T7 |

Estados de `ts.turno` observados: `LIBRE`, `RESERVADO`, `OTORGADO`, `RECEPCIONADO`, `SUSPENDIDO`, `INHIBIDO`, `REASIGNADO`.
