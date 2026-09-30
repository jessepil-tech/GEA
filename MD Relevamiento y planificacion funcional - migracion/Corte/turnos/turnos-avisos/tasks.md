---
title: Tasks — T7 · avisos de turno
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-avisos.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 D-TUR-76 (Francisco 2026-09-22)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify (2026-09-22)
3. [x] **TSK-ops-g0-3** COUNT `mensaje_turno` / `mensaje_turno_vencido` = **0+0**; centro 1001 `id_server_mail`/`id_server_sms` **null**; `cuerpo_mail_reasigna_turno` **null**; hab 90001 `envia_mail_reasigna_turno` **null**, `envia_sms_turno=N`; `MailTurnoJob` dump `ACTIVA=N` `MINUTOS/2`
4. [x] **TSK-app-g2-1** Port `GENERA_MAILS.f_mail_reprogramacion_turno` / `f_sms_reprogramacion_turno` (golden)
5. [x] **TSK-app-g2-2** Enganche archivo T6 *antes* del DELETE + housekeeping 7 d / server null
6. [x] **TSK-app-g2-3** POST despachar + `@Scheduled` + flag dedicado `enable-mail-turno`
7. [x] **TSK-app-g2-4** Golden (2 tests) + dos actores mismo criterio; IT 401 (QuarkusTest / Docker según entorno)
8. [x] **TSK-web-g4-1** N/A (sin hoja)
9. [x] **TSK-web-g5-1** Playwright **N/A**
10. [x] **TSK-ops-g6-1** Smoke Francisco «ok si veo bien las consultas» 2026-09-22 (`id_mensaje_turno_vencido=36222854`; `19999101` en `turno_vencido`)
11. [x] **TSK-ops-g6-2** Verify PASS + `verificar-sdd.sh turnos-avisos`

**Prohibido:** clonar WAR SCHEDULER; seed de `mensaje_turno` / `mensaje_turno_vencido`; gateway SMTP/SMS; ticket PDF; `EnvioMailsJob`; `ProcesarSmsJob`; WhatsApp; confirmación/cancelación/recordatorio al otorgar; backfill de `17255210`; acoplar a `DataCleanupJob` o al flag de T6.
