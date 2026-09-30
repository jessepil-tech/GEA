---
title: Plan — T5.1 hijo popups info north
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-popups
---

# Plan — Info popups north

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Ninguno v1. Doc req **no** Flyway → diferido |
| API | Reusar GET convenios/planes T5.1. Solo G2 si falta persistir `descripcion` en north |
| Web | Extender `turnos-agenda.component.ts` + 1–2 dialogs standalone |
| Tests | e2e ampliar `turnos-agenda.spec.ts` |

Orden: Clarify **FIRME** ✅ → G0 inventarios ✅ → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME + inventarios copy/interacción/validaciones/DDL |
| G1 | N/A (sin Flyway) |
| G2 | Persistir `descripcion` convenio en north — **done 2026-09-08** (sin endpoint nuevo) |
| G3 | Gate UI: botones 32×32 misma fila + geometría dialogs — **done 2026-09-08** |
| G4 | Dialogs + highlight — **done 2026-09-08**; auto-popup **WAIVE** D-TUR-39 |
| G5 | e2e abrir info búsqueda + info convenio — **done 2026-09-08** |
| G6 | Smoke stack real (abrir ambos en Agenda) |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| PNG HIS no versionado | Copiado a `Hospital-Web/public/images/turnos/InfoBusquedaPac.png` |
| HTML obs con markup | Sanitizar o `innerHTML` solo del campo API |
| Auto-popup al buscar | **WAIVE** D-TUR-39 — HIS Agenda no abre el dialog |
| Chocar con `@Output() select` | No nombrar output `select` |

## Fuera del plan v1

Filas doc req (DDL); WS elegibilidad; info prestación; historial convenios.
