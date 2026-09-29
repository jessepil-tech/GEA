---
title: Plan — T5.1d cobros agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-cobros
---

# Plan — Cobros agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Convenio/plan default: gap `param_general` vs `ts.call_center.id_convenio_dflt` (G1). CTA/coseguro = columnas ya en turno si existen; si no, display null. |
| API | Extender validar elegibilidad (outcome rechazo con convenio dflt) y/o GET saldo/coseguro lectura. |
| Web | Popup obs paciente; swap north; dialogs pagar/saldo; filas infoTurno. |
| Tests | Ampliar `e2e/turnos-agenda.spec.ts` |

Orden: Clarify **FIRME** → G0 inventarios → G1 gaps DDL → G2 API → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

Clarify **FIRME** 2026-09-09.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME + inventarios |
| G1 | Convenio dflt resoluble en `ts` (documentar gap) |
| G2 | API outcome rechazo + lecturas saldo/coseguro |
| G3 | Gate UI: popup 477 / pagar 446 / saldo 462 / infoTurno 97 |
| G4 | Web: rechazo cambia north + dialogs |
| G5 | e2e rechazo → popup + convenio default |
| G6 | Smoke stack real |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Meter caja / asientos | Rechazar; diferido facturación |
| Cerrar P-ORA-010 | Verify: sigue abierto |
| Swap convenio sin seed dflt | Fixture `id_convenio_dflt` en call center demo |
| Saltar Gate UI | xhtml **antes** de template |

## Fuera del plan v1

WS HTTP; T6; ABM param_general; recetas reales.
