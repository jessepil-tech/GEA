---
title: Spec — T7 · avisos de turno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-avisos
---

# Spec — Avisos de turno

Plan origin T0–T7: T7 = side-effects (`mensaje_turno` + scheduler; ticket PDF; APIs).  
T6 archivo **gate-done** sin plantillas. Este slug cobra **avisos**. Ticket PDF / APIs → hijos.  
Clarify **FIRME Camino 1** — 2026-09-22 D-TUR-76 (Francisco «ok firme»). T7 se puede sin servicio real de mail/SMS.

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md).

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto, `cliente="TS"`) |
| Ramas `esClienteX()` | ninguna en `f_mail_reprogramacion_turno` / `f_sms_reprogramacion_turno` |
| Decisión | camino genérico · resto `diferido(multi-instalacion)` |

## Problema

Al archivar un `OTORGADO`, el HIS arma mail/SMS `REPROGRAMACION` (`GENERA_MAILS`) y `MailTurnoJob` despacha la cola `mensaje_turno` / `mensaje_turno_vencido`. T6 copia mensajes **ya existentes** y no genera los nuevos. El paciente no es avisado. El WAR `SCHEDULER` está **fuera de alcance**; la capacidad no.

El centro 1001 del dump **no** tiene `id_server_mail` / `id_server_sms` ni cuerpo de reasignación. El HIS, en ese estado, **no envía** y puede no **generar**. Camino 1 porta esa paridad: cola + plantilla + job; SMTP real no es DoR.

## Resultado (objetivo Camino 1)

1. Al archivar (enganche T6, *antes* del DELETE como el BODY): si `OTORGADO` y hab/centro dicen que sí, INSERT `mensaje_turno_vencido` tipo `REPROGRAMACION` (mail y/o SMS) con texto de `GENERA_MAILS`.
2. `MailTurnoJob` en Api: `@Scheduled` + POST G6. Por centro: pendientes E-MAIL/SMS. Si `id_server_*` es null → **no** llama SMTP (warn HIS `SERVER_*_NO_CONFIGURADO_CENTRO`); no inventa un gateway.
3. Housekeeping del SP: `enviado='S'` a los 7 días y si el centro no tiene server (ventana `fecha_hora_a_enviar`).
4. Flag dedicado (no `enable-background-jobs`). Intervalo HIS: `tarea_programada` MailTurnoJob `MINUTOS/2`, `ACTIVA=N` en el dump — el ABM sigue `diferido(disparador)`.

Fuera: ticket PDF turno (sidecar; hijo); confirmación/cancelación/recordatorio al otorgar/liberar; `ProcesarSmsJob`; WhatsApp; `EnvioMailsJob` (otra cola); SMTP/SMS de verdad → `diferido(smtp)`.

## Clarify — **FIRME Camino 1** (2026-09-22)

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T6 **gate-done**. Tablas `mensaje_*` en dump (0 filas). | A9 · T6 |
| 2 | ¿Misma familia UI? | **No hay hoja.** Semilla = `MailTurnoJob`. Gate UI **N/A**. | `--jobs mail` |
| 3 | ¿Happy path? | OTORGADO archivado con flags S + cuerpo → nace fila `REPROGRAMACION` `enviado='N'`. Job: si no hay server, no SMTP; housekeeping puede marcar `S`. Demo actual (flags null) → **0 altas** es paridad. | BODY 9627–9773 · MailTurnoJob.java · 9814–9863 |
| 4 | ¿Ciclo de vida? | Alta en el archivo (sigue vivo en `ts.turno` en el HIS). No backfill de `17255210` ya archivado. Despacho no borra el mensaje: marca `enviado` / `fecha_hora_envio`. | loop `c_vencido` *antes* del DELETE |
| 5 | ¿Errores / permisos? | Jobs interfaz, sin `rol_funcional_pers`. POST JWT; sin token → 401. Flag off → no-op. PASS admin no cuenta. Warn server no configurado = contrato. | Job.java |
| 6 | ¿Side-effects? | **Portar** alta REPROGRAMACION + despacho + housekeeping. **No** `EnvioMailsJob`. **No** ticket PDF. T6 archivo no se reescribe (se engancha). | `--jobs mail` |
| 7 | ¿Paridad UI / Gate? | **N/A**. | — |
| 8 | ¿Viaje Playwright? | **N/A** (proceso sin acto de usuario en Web). Evidencia = golden + IT + G6 SQL. | `regla-playwright-migracion.md` |
| 9 | ¿Fuera? | Ticket PDF turno. Confirmación/cancelación/recordatorio. SMS inbound. WhatsApp. SMTP real. D-TUR-17. T1 ABM. `consultaPreagenda`. | slugs abajo |

**Caminos**

| Camino | Qué entra | Por qué |
|--------|-----------|---------|
| **1 (propuesto)** | Nace `REPROGRAMACION` al archivar + `MailTurnoJob` (skip SMTP si no hay server) + housekeeping 7 d / server null | Techo; Francisco: T7 sin servicio real; Demo no tiene server |
| 2 | Todas las plantillas `GENERA_MAILS` de turno (confirmación, cancelación, recordatorio, econsulta) + WhatsApp + ticket PDF | Excede techo; mezcla otorgar con job |
| Descartado: exigir SMTP/SMS de piloto | — | El HIS ya opera sin server |
| Descartado: clonar WAR SCHEDULER | — | Misma regla T6 |
| Descartado: `EnvioMailsJob` en este slug | — | Cola `mail` genérica, no `mensaje_turno` |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Alta `REPROGRAMACION` al archivar OTORGADO | `f_migra` + `GENERA_MAILS.*` | **In scope** Camino 1 | **Sí** |
| Despacho `MailTurnoJob` | Job.java | **In scope** (sin SMTP si server null) | **Sí** UPDATE |
| Housekeeping 7 d / server null | BODY 9814–9863 | **In scope** | **Sí** |
| SMTP/SMS real | `Interfaces.envioMailTurno` / `envioSMSTurno` | **diferido(smtp)** | — |
| Confirmación / cancelación / recordatorio | otras `f_mail_*` al otorgar/liberar | **diferido** hijo | — |
| `ProcesarSmsJob` / `RecepcionSmsJob` | ENVIO_MAIL_SMS | **diferido** | — |
| WhatsApp | `EnvioWhatsappJob` | **diferido** | — |
| `EnvioMailsJob` / `EnvioSmsJob` | cola `mail`/`sms` genérica | **N/A** este circuito | — |
| Ticket PDF turno (sidecar) | `f_imprime_turno_pac` | **diferido** hijo | — |
| ABM `tarea_programada` | menú 10294 | **diferido(disparador)** | — |

## Firmas PL/SQL

| Firma | Decisión | Nota |
|-------|----------|------|
| `GENERA_MAILS.f_mail_reprogramacion_turno` | **portar** (golden) | lee `ts.turno` (HIS pre-DELETE); migrado: mismo momento o `turno_vencido` si ya archivó |
| `GENERA_MAILS.f_sms_reprogramacion_turno` | **portar** (golden) | — |
| Resto avisos `TURNOS.f_migra_turno_vencido` (rama OTORGADO + housekeeping) | **portar** | T6 no lo cobró |
| `GENERAL.f_next_id_tabla('MENSAJE_TURNO')` | **reusar** `NextIdService` | ya en Api |
| `Interfaces.envioMailTurno` / `envioSMSTurno` | **diferido(smtp)** | Camino 1 no llama gateway |

Casos golden mínimos: flags N/null → no alta; flags S + cuerpo → HTML/SMS no vacío; paciente sin mail; `id_turno` ya vencido; NULL paciente.

## Acceso y trazabilidad

Perfiles menú: **ninguno**. Rol funcional PL/SQL: **ninguno**.  
Prueba negativa: POST sin JWT → 401. Flag off → 0 escrituras.  
Auditoría: `mensaje_*` si están en `TBL_AUD_*` → **`diferido(auditoria)`** si el corte escribe y el destino no audita.

## Presupuesto no funcional (paso 3)

| Eje | Presupuesto | Cómo se mide |
|-----|-------------|--------------|
| Tiempo | p95 POST despacho ≤ 1,5 s en cola de prueba | percentil |
| Volumen | COUNT `mensaje_*` pendientes del dump; si 0 → «medido en vacío» + `diferido(perf-volumen)` | ahora 0+0 |
| Concurrencia | dos POST despacho sobre el mismo `id_mensaje` | SKIP del scheduler + UPDATE; evidenciar que no se envía doble |

## Decisiones

| ID | Decisión |
|----|----------|
| D-TUR-76 | Camino 1 **FIRME**: nace `REPROGRAMACION` + MailTurnoJob sin SMTP si no hay server + housekeeping; no ticket PDF; no EnvioMailsJob; no inbound SMS. |
