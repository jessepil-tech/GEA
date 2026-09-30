---
title: Verify — T5.1 Turnos agenda ficha paciente
version: 0.3.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-ficha-paciente
---

# Verify — T5.1 · Camino 1

**Gate:** **PASS / gate-done** 2026-09-08 · Clarify **FIRME** Camino 1 **2026-09-07** · G0–G6 (e2e PASS; smoke stack real **PASS** ops 2026-09-08).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Buscar paciente (apellido/doc) | **done G4** | `GET …/pacientes/buscar` · `paciente-buscador-dialog` |
| Buscar convenio + plan | **done G4** | `GET …/convenios/buscar` · `…/planes` · `convenio-buscador-dialog` |
| Limpiar paciente | **done G4** | `limpiarPaciente()` — north + west + `diasMap` |
| Ficha west read-only | **done G4** | `GET …/pacientes/{id}/ficha` |
| Desde / Hasta | **done G4** | `app-grilla-time-picker` (mismo T4) |
| Leyenda calendario 6 colores | **done G4** | west + almanaque + grilla `gt-*` HIS · dark |
| Otros centros (lista) | **done G4** | `GET …/otros-centros` |
| Asignar desde otros centros | **done G4** | gear → reserva T5 |
| Información turno otros centros | **done G4** | gear → dialog infoTurno T5 |
| Info búsqueda paciente (fa-info) | **done T5.1b** | [`turnos-agenda-info-popups/`](../turnos-agenda-info-popups/) **gate-done** 2026-09-10 |
| Info convenio / obs plan | **done T5.1b** | mismo |
| Accordion tab Paciente 170px | diferido | `turnos-asignacion-shell` |
| ABM datos paciente tabs | diferido | `datosPaciente.xhtml` |
| Nuevo paciente | diferido | ABM pacientes |
| Elegibilidad WS (icono) | **done T5.1c** seed; WS **P-ORA-010** | [`turnos-agenda-elegibilidad-cobros/`](../turnos-agenda-elegibilidad-cobros/) |
| Drag-drop ficha | diferido | hijo drag-drop |
| Consultar agenda otros centros | diferido | `turnos-agenda-consultas` |
| T5 núcleo grilla/menú gear | **heredado done** | no regresión |

## Viaje Playwright

| Viaje | Decisión | Spec |
|-------|----------|------|
| Buscar paciente + ficha west | **e2e-migrado** | `Hospital-Web/e2e/turnos-agenda.spec.ts` |
| Doble clic filtro buscador no cierra modal | **e2e-migrado** | mismo spec |
| Regresión T5 otorgar/liberar | **e2e-migrado** | mismo spec |
| Legacy HIS | **no** | — |

E2E mocks CI ≠ smoke stack real (G6).

## Paridad UI (xhtml)

Inventarios G0: **done 2026-09-07** · Gate UI G3 **PASS** ops (iteración 2026-09-08: buscadores, time picker, colores HIS, grilla `table-fixed`, output `accept` vs `select` nativo).

| Inventario | Estado |
|------------|--------|
| [`inventario-copy-msg.md`](inventario-copy-msg.md) | **done G0** |
| [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md) | **done G0** · Web G4 |
| [`inventario-validaciones.md`](inventario-validaciones.md) | **done G0** |
| [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md) | **done G0** · Flyway extra **N/A** (V31/V47) |

Web: `turnos-agenda.component.ts` · `paciente-buscador-dialog` · `convenio-buscador-dialog` · Hospital-Web `0396f18`.  
API: `TurnosAgendaFichaResourceIT` (PG `HOSPITAL_PG_*`).

## Smoke

**G6 stack real — PASS 2026-09-08** (smoke manual ops, mismo stack T5):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Identity `:8080` · Api `:8081` · Web `:4200` · PG `grupogea-hospital_dev-test` · Reports N/A | **PASS** |
| 2 | `/turnos/inicio` → call center sesión | **PASS** |
| 3 | `/turnos/agenda` → buscador paciente (`20001`) → ficha west | **PASS** |
| 4 | Convenio/plan (`5001` / `1`) → Consultar grilla | **PASS** |
| 5 | Menú gear → Asignar → Otorgar · Liberar motivo `91004` | **PASS** (núcleo T5 en la misma pantalla) |

Convivencia seed AGI: fecha/rango dedicado — no mezclar con OTORGADO legacy sin coordinar.

## Resultado

**PASS / gate-done T5.1** con diferidos explícitos (info popups north, accordion 170px, ABM paciente, elegibilidad WS, drag-drop, consultar agenda otros centros).  
Siguiente corte Turnos: T6 padre `turnos-ciclo-vida` diferido. T5.1b–d y T6.1 **gate-done** 2026-09-10.
