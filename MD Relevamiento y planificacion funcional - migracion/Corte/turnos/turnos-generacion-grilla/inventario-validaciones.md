---
title: Inventario validaciones T4 — MessageBundle / BB / SP
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-generacion-grilla.validaciones
---

# Inventario validaciones — T4 generación grilla (G0)

Plantilla gate: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.4+.

Fuente:

- BB: `BBGeneracionGrillaTurnos` · `BBEliminarGrillaTurno` · `BBConsultaAgendasGeneradas`
- Mensajes: `MessageBundle.java` (HOSPITAL-BUSINESS, textos inline ES)
- ImpBus: `ImpBusTurno` — **pass-through** al SP (sin reglas propias)
- SP: `TS.TURNOS.f_gen_grilla_turnos` / `f_elim_grilla_turnos` / `f_consulta_agendas_generadas`

Web destino: validators + toast · API: `TurnosGeneracionValidation` (nombre a confirmar en G2).

**Feedback UI:** `BusinessException` / MessageManager → **toast** (WARN/INFO). Tabla observaciones post-generar = contrato.

| Regla | Mensaje / lógica legacy | UI | API |
|-------|-------------------------|----|-----|
| GEN: EQUIPO sin equipo | Debe seleccionar un equipo. (`REQUIRED_EQUIPO`) | diferido D-TUR-17 | diferido |
| GEN: PERSONAL sin personal | Debe seleccionar un personal. (`REQUIRED_PERSONAL`) | pendiente | pendiente |
| GEN: centro null | Debe seleccionar un centro de atención. (`REQUIRED_CENTRO_ATENCION`) | pendiente | pendiente |
| GEN: SERVICIO sin servicio | Debe seleccionar un servicio. (`REQUIRED_SERVICIO`) | pendiente | pendiente |
| GEN: fechas &lt; hoy | La fecha desde y la fecha hasta no deben ser menores a la fecha de hoy. (`WRONG_INTERVAL_DATE_15`) | pendiente | pendiente |
| GEN: hasta &lt; desde | La fecha hasta debe ser posterior a la fecha desde (`WRONG_INTERVAL_DATE_3`) | pendiente | pendiente |
| GEN: mismo día, hora hasta ≤ desde | El horario hasta debe ser posterior al horario desde (`WRONG_INTERVAL_HOUR`) | pendiente | pendiente |
| GEN: grupo con ctd simultáneos &lt; 1 | El grupo está configurado sin turnos. (`EL_GRUPO_ESTA_CONFIGURADO_SIN_TURNOS`) | pendiente | pendiente |
| GEN: algún grupo ctd=0 (sin filtro grupo) | Existen grupos configurados con cantidad turnos simultáneos igual a 0. (`EXISTEN_GRUPOS_CONFIGURADOS_SIN_TURNOS`) | pendiente INFO | pendiente INFO |
| GEN: personal solape otro centro | El personal tiene turnos solapados para otro centro de atención. (`EL_PERSONAL_TIENE_TURNOS_OTRO_CENTRO`) | pendiente INFO | pendiente INFO |
| GEN: fin OK | El proceso ha finalizado con éxito. Revise las observaciones si existieran. (`PROCESO_FINALIZADO_CON_EXITO_REVISE_OBSERVACIONES`) | pendiente INFO | pendiente |
| ELIM: centro (modo serv) | Debe elegir el Centro de Atención. (`CENTRO_ATENCION_REQUIRED_ERROR`) | pendiente | pendiente |
| ELIM: servicio | Debe elegir el Servicio. (`SERVICIO_REQUIRED_ERROR`) | pendiente | pendiente |
| ELIM: personal | Debe seleccionar un personal. (`PERSONAL_REQUIRED_ERROR`) | pendiente | pendiente |
| ELIM: equipo | Debe seleccionar un equipo. (`EQUIPO_REQUIRED_ERROR`) | diferido D-TUR-17 | diferido |
| ELIM consultar: fechas &lt; hoy | `WRONG_INTERVAL_DATE_15` | pendiente | pendiente |
| ELIM consultar: hasta &lt; desde | `WRONG_INTERVAL_DATE_3` | pendiente | pendiente |
| ELIM: sin turnos selected | Debe seleccionar al menos un turno (`DEBE_SELECCIONAR_AL_MENOS_UN_TURNO`) | pendiente | pendiente |
| ELIM: confirm | La agenda se eliminará y los turnos otorgados quedarán pendientes… (`msg` Resources) | pendiente | — (solo UI) |
| CONS: centro | `REQUIRED_CENTRO_ATENCION` | pendiente | pendiente |
| CONS: servicio / personal / equipo | `REQUIRED_SERVICIO` / `REQUIRED_PERSONAL` / `REQUIRED_EQUIPO` | pendiente (equipo diferido) | pendiente |
| CONS imprimir OK | Se completo el trabajo de impresión. (`PRINT_JOB_COMPLETE_INFO`) | pendiente INFO (toast post-descarga) | N/A (Reports devuelve PDF; sin callback impresora) |

## Observaciones SP (`f_gen_grilla_turnos` → contrato lista)

| Patrón | Ejemplo texto |
|--------|---------------|
| Post fin vigencia hab | La fecha (dd/MM/yy) es mayor a la fecha fin vigencia de la habilitacion de turnos. |
| Fecha &lt; hoy | La fecha (dd/MM/yy) es anterior a la fecha actual |
| Feriado | La fecha (dd/MM/yy) esta marcada como feriado |
| Solape grupos | Existen grupo de horarios solapados. |
| Ya generados | La fecha (dd/MM/yy) tiene turnos generados para el grupo {nombre} |
| Sin config | La fecha (dd/MM/yy) no tiene configurado grupos con horarios vigentes… |

`f_elim_grilla_turnos`: no agrega MessageBundle; reasigna otorgados → `turno_a_reasignar` (popup ELIM).

## Checklist anti-omisión

- [ ] Validaciones BB create **y** update path equivalentes en API (comandos generar/eliminar)
- [ ] Observaciones SP mapeadas a DTO (no silenciar)
- [ ] Toast severities: WARN bloquea; INFO no bloquea (ctd=0 / solape otro centro / fin OK)
- [ ] Equipo: filas `diferido` explícitas (no omitir)
- [ ] SMS/mail eliminar: diferido T7 (D-TUR-19)

## Filtros required (resumen)

| Pantalla | Required |
|----------|----------|
| GEN | centro siempre; servicio|personal según radio; fechas ≥ hoy; horas si mismo día |
| ELIM | según radio; fechas; ≥1 turno al eliminar + confirm |
| CONS | centro; servicio|personal según radio; año |
