---
title: Plan — T5 Turnos agenda / otorgar
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-otorgar
---

# Plan — T5

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | Flyway V46+: gaps G0 (`ctrl_turnos_pac` si se cobra tope; next_id hist ya V43). **No** `tmp_turno*` |
| Core | `TurnosAgendaPort` + transiciones estado + lock sesión (D-TUR-28) |
| Application | CQRS lectura + `ReservarTurno*`, `OtorgarTurno*`, **`TomarTurno*`** (poll), `LiberarTurno*`, `ReservarSobreturno*`, **`ExpirarReservas*`** |
| Infrastructure | JDBC sobre `ts.turno` / `hist_turno` / hab T2 / maestros V31; **sin** Oracle SP |
| Presentation | `/api/v1/turnos/agenda/...` incl. **`POST …/tomar`**, **`POST …/expirar-reservas`** |
| Web | `/turnos/agenda` · dialog info turno: **interval ~59 s** → tomar · use-case · buscadores si coinciden |
| Tests | Golden transiciones + IT serv+pers; fixture T4 LIBRE + paciente/convenio demo |

Orden: Clarify **FIRME** → inventarios G0 → Flyway G1 → golden+port lectura → escritura → API → **Gate UI** → smoke → e2e → verify.

## Cortes internos

| Corte | Exit |
|-------|------|
| G0 | Clarify FIRME + inventarios copy/validaciones/DDL (borrador) |
| G1 | Flyway V46 seed `LIBERACION_TURNO` · `ctrl_turnos_pac` diferido — **done 2026-09-07** |
| G2 | Query grilla día + días disponibles + API GET + IT — **done 2026-09-07** |
| G3 | Port reserva + tomar (lock A) + expirar + API POST + IT — **done 2026-09-07** (inhibe JDBC → cuando exista `inhab_tur_*`) |
| G4 | Port otorga + libera + sobreturno + hist + API + IT — **done 2026-09-07** (libera sin merge slots; tope sobreturno/hab defer) |
| G5 | UI agenda + dialog info turno con **poll 59 s → tomar** — **done 2026-09-07** (buscador paciente/convenio → ficha v1) |
| G6 | Smoke stack real (checklist ops pendiente) + e2e Playwright **PASS** + verify PASS + backlog/matriz — **done 2026-09-07** |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Body `f_get_grilla_dia` ~3.7k LOC | Golden por escenario (LIBRE/OTORGADO, hab, tope, filtro estado); no clonar tmp |
| Confundir HOS-APP con HIS | D-TUR-22; no `inicioAgenda` |
| Mezclar T6 reasignar | Rechazar en PR; solo libera |
| Elegibilidad real | Hijo + P-ORA-010; v1 stub |
| Convivencia seed AGI OTORGADO | Smoke en rango/fecha dedicado (como T4) |
| Equipo colado | Radio/filtro disabled D-TUR-17 |
| Concurrencia lock | Golden choque 3 min + IT dos operadores; D-TUR-28 |

## Dependencias

- T4 gate-done (oferta `LIBRE`)
- T2 hab + T3 grupos
- T1 call center JWT
- `ts.turno` V31 · `hist_turno` V43
- Buscadores hab (gate-done) — reusar solo si el popup de agenda es el mismo

## Fuera del plan T5 v1

Equipo; repetidos/múltiples/pre-agenda; ficha west; WS elegibilidad; cobros; **Quartz job** (expiración v1 = consulta + POST); T6; T7; HOS-APP.
