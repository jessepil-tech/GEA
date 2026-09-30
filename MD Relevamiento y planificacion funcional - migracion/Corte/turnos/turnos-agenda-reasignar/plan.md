---
title: Plan — T6.1 reasignar agenda
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-reasignar
---

# Plan — Reasignar agenda

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | **Sin** columna `PENDIENTE_LIBERAR` en `ts.turno`. Sin Flyway de estado. Origen sigue `OTORGADO` hasta el cierre. |
| API | Sesión de reasignar es **Web**. Extender otorga T5: `idTurnoOrigen` opcional → libera origen (paridad `otorgarTurnoPac(turno, turnoAReasignar)`). |
| Web | Menú fila REASIGNAR/CANCELAR; overlay; north paciente; toast; popup obs 650px. Misma `/turnos/agenda`. |
| Tests | Ampliar `e2e/turnos-agenda.spec.ts` (3 viajes). |

Orden: Clarify **FIRME** → G0 inventarios → G1 gaps DDL (documentar no-columna) → G2 API origen opcional → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

Clarify **FIRME** 2026-09-09.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME + inventarios |
| G1 | Documentar: overlay UI-only; sin ALTER `ts.turno` |
| G2 | Otorga con origen opcional libera el slot origen |
| G3 | Gate UI: menú L279–284 + popup L122 **antes** de template |
| G4 | Web: sesión + overlay + north + popup + cierre |
| G5 | e2e: reasignar / cancelar / asignar libera origen |
| G6 | Smoke stack real **PASS** 2026-09-10 |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Persistir `PENDIENTE_LIBERAR` | Prohibido; verify NFR-1 |
| Meter cola `turnosAReasignar` | Rechazar; diferido T6 |
| Fork de ruta | Prohibido; misma `/turnos/agenda` |
| Saltar Gate UI | xhtml **antes** de template |
| Liberar origen sin otorga nuevo | Solo cierre Camino 1; cancelar no toca BD |
| T7 mail al otorga | No este slice |

## Fuera del plan v1

Cola T4; suspender; reemplazo profesional; vencidos; hist pantalla; T7.
