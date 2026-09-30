---
title: Plan — T5.1c elegibilidad north + doc req
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros
---

# Plan — Elegibilidad north + doc req

## Enfoque

| Capa | Decisión (Camino 1) |
|------|---------------------|
| DDL | Flyway `ts.doc_requerido` + `ts.doc_req_plan_conv` + `ts.doc_req_prest_plan`. Flags convenio/plan ya en V31. |
| API | GET doc-req agenda; comando/query validar elegibilidad reusando puerto seed (no VALIDADORES.jar). |
| Web | North afiliado/doc/icono/spinner; cablear `listDocReq` al dialog T5.1b. |
| Tests | Ampliar `e2e/turnos-agenda.spec.ts` |

Orden: Clarify **FIRME** (2026-09-08) → G0 inventarios → G1 Flyway → G2 API → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME + inventarios copy/interacción/validaciones/DDL |
| G1 | Flyway `ts.doc_req_*` (tipos PG documentados) |
| G2 | GET doc-req + validar elegibilidad (seed) |
| G3 | Gate UI: fila afiliado+icono; máscara; spinner |
| G4 | Web north + tabla info convenio con filas |
| G5 | e2e autorizado / rechazo / doc req |
| G6 | Smoke stack real |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Confundir seed con P-ORA-010 cerrado | Verify: P-ORA-010 **sigue abierto** |
| Copiar `public.doc_req_*` de Reports | DDL canónico `ts` |
| Meter pagar/CTA en el mismo PR | Rechazar; hijo cobros |
| Convenio demo sin flag | Fixture nombrado (seed + `req_valid_elegibilidad`) |

## Fuera del plan v1

WS HTTP; `turnos-agenda-cobros`; info prestación; T6.
