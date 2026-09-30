---
title: SDD — T7 · avisos de turno (cola + despacho)
description: >-
  Alta REPROGRAMACION al archivar OTORGADO, MailTurnoJob y housekeeping 7 d.
  Sin SMTP/SMS real si el centro no tiene server. Sin ticket PDF.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-avisos
---

# T7 — Avisos de turno (`turnos-avisos`)

**Estado:** **gate-done** 2026-09-22 · Clarify **FIRME Camino 1** D-TUR-76 (Francisco «ok si veo bien las consultas»).  
Cierra el último número del plan origin T0–T7 (avisos). Ticket PDF turno y APIs externas → hijos.  
Capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) A9.  
Jobs: [`relevamiento-procesos-programados/`](../../../relevamiento/relevamiento-procesos-programados/).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).  
Loop: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
Padre T6 [`turnos-ciclo-vida/`](../turnos-ciclo-vida/) **gate-done** (archivo; **no** plantillas).  
Instalación de referencia: Call Center Demo. `GENERA_MAILS` reprogramación **sin** `esClienteX()`.

Índice (2026-09-22): `--jobs mail` → `MailTurnoJob` (semilla) · `EnvioMailsJob` / `EnvioSmsJob` (mailbox genérico, **fuera**) · `ProcesarSmsJob` / `RecepcionSmsJob` / WhatsApp (**diferidos**). Semilla = job (no `ACCION` de menú). Techo: 0 hojas / 0 beans HIS / 4 firmas / 0 reportes.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | turnos | este workspace |
| BODY / package | **GENERA_MAILS** + resto avisos `TURNOS.f_migra` | **liberado** (gate-done) |
| Rango Flyway | **n/a** (tablas en dump: `mensaje_turno`, `mensaje_turno_vencido`, `mail_persona`, `hab_turnos_*`, `centro_atencion`) | — |
| Tablas `ts` que escribe | `mensaje_turno` / `mensaje_turno_vencido` (INSERT alta + UPDATE `enviado` / `fecha_hora_envio` / `ctd_intentos`) | — |
| Rama | `dev/t7-turnos-avisos` (Api) · `dest/t7-turnos-avisos` (Migration; Web **n/a**) | **reservada** |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Centro 1001 · servicio 10 · personal 90001 | no | T1/T2 | vigente |
| `id_server_mail` / `id_server_sms` en 1001 | no | dump | **null** — Camino 1 no exige SMTP |
| `cuerpo_mail_reasigna_turno` / `sms_reasigna_turno` en 1001 | no | dump | **null** — HIS **no** genera REPROGRAMACION |
| `hab_turnos_pers_serv.envia_mail_reasigna_turno` 90001 | no | dump | **null** (SMS hab = `N`) |
| OTORGADO archivado `17255210` | no | T6 job 2026-09-22 | ya en `turno_vencido`; **sin** backfill (HIS genera *antes* del DELETE) |
| G6 alta REPROGRAMACION | padres hab+cuerpo (revertidos) | acto de config, no seed de `mensaje_*` | **hecho** 2026-09-22 · `id_mensaje_turno_vencido=36222854` |

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **7** cerrado · push pendiente (no pedido) |
| Reservas | GENERA_MAILS + resto avisos `f_migra` **liberado** · Flyway n/a · ramas T7 |
| Universo firmado | Camino 1 (inventarios de este slug) |
| Fixture | padres revertidos; OTORGADO `19999101` archivado; aviso `36222854` |
| Evidencia | [verify-report.md](verify-report.md) · 8/8 **verificado** |
| Diferidos abiertos | ticket PDF turno **cobrado** [`turnos-agenda-imprimir-turno/`](../turnos-agenda-imprimir-turno/) · confirmación/cancelación/recordatorio al otorgar · `ProcesarSmsJob` / `RecepcionSmsJob` · WhatsApp · `EnvioMailsJob` mailbox · SMTP real `diferido(smtp)` · `diferido(disparador)` · `diferido(auditoria)` · `diferido(perf-volumen)` · `diferido(perf-tiempo)` |
| Próximo paso | ninguno de este corte · D-TUR-17 / `consultaPreagenda` / alta ATENCION |

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance + Clarify **FIRME** Camino 1 |
| [plan.md](plan.md) | G0–G6 (sin hoja Web) |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger 8/8 **verificado** |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | Sin Flyway |

**No es** clonar WAR `SCHEDULER`. **No es** `EnvioMailsJob` (cola `mail` genérica). **No es** ticket PDF. **No es** T6 archivo. **No es** D-TUR-17.
