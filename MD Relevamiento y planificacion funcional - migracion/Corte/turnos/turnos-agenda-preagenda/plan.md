---
title: Plan — T5.6 pre-agenda turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-preagenda
---

# Plan — Pre-agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | G1: `CREATE TABLE IF NOT EXISTS ts.pre_agenda_turno` (nombres Oracle TS; tipos catálogo). Secuencia **ya** `sec_id_pre_agenda_turno` V10. FK solo a padres Flyway (`paciente` / `servicio` / `prestacion` si existen). **Sin** FK a `det_prescrip_*`. Dump remoto ya tiene la tabla: `IF NOT EXISTS`. |
| API | **GET** lista PENDIENTE (CQRS Query). Filtros opcionales: paciente, servicio, convenio, plan, fechas. Labels: persona / servicio / prestación; convenio/plan via EXISTS `atencion_amb` / `internacion` (tablas dump; LEFT — null si no hay). Extender **T5 otorgar**: body opcional `idPreAgendaTurno` → UPDATE `id_turno` + `OTORGADO`. Resource delgado. |
| Web | Vista `preagenda` en `turnos-agenda`: accordion live; north+tabla; Asignar → `vista=agenda` + prefijo north + consultar. Sin calendario. Sin south. |
| Tests | Ampliar `e2e/turnos-agenda.spec.ts` |

Orden: Clarify FIRME → G0 **docs 2026-09-15** → G1 Flyway si falta → G2 API → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify **FIRME** + inventarios — 2026-09-15 |
| G1 | `V54` `ts.pre_agenda_turno` IF NOT EXISTS (próximo Vnn al FIRME; un writer Flyway) |
| G2 | Query JDBC + otorgar UPDATE + Resource + IT |
| G3 | Gate UI `preAgendaTurnos.xhtml` **antes** de template |
| G4 | Web: Turnero live + vista + Asignar → Agenda |
| G5 | e2e abre / toast fechas / filas / Asignar |
| G6 | Smoke Francisco UI + lista PENDIENTE + salto Agenda — **PASS** 2026-09-15 |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Confundir con `consultaPreagenda.xhtml` | Spec D-TUR-61; hijo `turnos-consultas-preagenda` |
| Portar insert ATENCION | Fuera; G6 usa filas dump o seed PENDIENTE |
| Convenio JOIN sin tablas Flyway | LEFT JOIN; CI seed sin convenio; G6 dump |
| Fork `/turnos/pre-agenda` | Prohibido |
