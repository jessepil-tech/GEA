---
title: Plan — T5.2 sobreturno agenda
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-sobreturno
---

# Plan — Sobreturno agenda

## Enfoque

| Capa | Decisión (Camino 2 FIRME) |
|------|---------------------------|
| DDL | Sin ALTER. `ts.turno.id_motivo_sobreturno` V31; motivo `SOBRETURNO` V35 `91002`. |
| API | Reusar `POST …/sobreturno` + otorga T5. G2: check grilla del día + turnos del paciente ese día vía `GET …/grilla` T5 (sin endpoint nuevo). Motivos: `GET …/motivos?tipoMotivo=SOBRETURNO` (T1). |
| Web | Accordion 170px + popup HIS; luego infoTurno T5. |
| Tests | Ampliar `e2e/turnos-agenda.spec.ts` |

Orden: Clarify **FIRME** → G0 inventarios → G1 gaps DDL → G2 N/A T5 → Gate UI G3 → Web G4 → e2e G5 → smoke G6.

Clarify **FIRME Camino 2** 2026-09-10.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME Camino 2 + inventarios |
| G1 | Documentar: sin Flyway nuevo; motivo seed vigente |
| G2 | N/A endpoint nuevo: `GET …/grilla` cubre “hay oferta” y turnos hoy |
| G3 | Gate UI: accordion L20–108 + menú L72 + popup L316 **antes** de template |
| G4 | Web: accordion + popup + Aceptar→otorga |
| G5 | e2e: toast / abre / otorga / turnos hoy |
| G6 | Smoke stack real (ops Francisco) |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Implementar ABM Paciente / otras `.faces` | Chrome disabled + slug; no navegar |
| Habilitar equipo | D-TUR-17 disabled |
| Meter caja / T7 | Fuera |
| Saltar Gate UI | xhtml **antes** de template |
| Confundir API T5 con UI done | Verify: UI + accordion era el gap |
| Sobreturno en gear | Prohibido — HIS es Turnero |

## Fuera del plan v1

ABM Paciente; múltiples; pre-agenda; T6 padre; T7; lista espera; ABM motivos.
