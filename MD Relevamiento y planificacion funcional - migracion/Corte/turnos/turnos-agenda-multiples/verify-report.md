---
title: Verify — turnos múltiples agenda
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples.verify
---

# Verify

**Gate:** **gate-done** 2026-09-14 · Clarify **FIRME** Camino 1. D-TUR-55 · D-TUR-56.  
G6 smoke ops **PASS** (Francisco, `/turnos/agenda` Turnero Múltiples).

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Turnero + vista carrito | **done** | spec RF-1–2 |
| Consultar días + tabla slots | **done** | RF-3 · tajos `mtos_duracion_tur` sobre LIBRE largo |
| Reserva lote + infoTurno | **done** | RF-4–5 · partir tajo mismo `id_turno` |
| Cambiar horario | **done** | [`turnos-agenda-multiples-cambiar/`](../turnos-agenda-multiples-cambiar/) |
| Equipo | **done** | [`turnos-agenda-multiples-equipo`](../turnos-agenda-multiples-equipo/) 2026-09-28 |
| Pre-agenda / consultas | **diferido** | slugs T5 |
| Print prep recepción | **N/A** | agenda call center / T7 |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | Turnero Múltiples → Agregar CONS → reelegir LAB → Agregar → Consultar → día → Asignar Turno → infoTurno tabla → otorga |
| Viaje 2 | Consultar sin convenio → toast `Debe seleccionar un Convenio.` |
| Viaje 3 | 2º Agregar sin reelegir prestación → 2ª fila (north no se limpia; ops 2026-09-14) |
| Viaje 4 | Trash último filtro o Limpiar Datos → limpia calendario + slots; trash con filtros restantes → reconsulta días + grilla |
| Fixture | mocks `turnos-agenda.spec.ts` |
| Legacy e2e | **no** |

E2E mocks ≠ G6. Universo de CU = esta matriz.

## Paridad UI (xhtml)

Inventarios G0: **hecho 2026-09-11**. Gate UI vista+popup **antes** de smoke.

## Smoke

**G6 stack real — PASS 2026-09-14** (ops Francisco, `/turnos/agenda`, visto bueno):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Turnero Múltiples → Agregar → Consultar → slots → Asignar lote (tajos 20 min sobre LIBRE 08:00–12:00) → infoTurno | **PASS** |
| 2 | 2º Agregar sin reelegir prestación acumula fila (north no se limpia) | **ops 2026-09-14** |

E2e: `Hospital-Web/e2e/turnos-agenda.spec.ts` describe `Turnos agenda — múltiples T5.4` (mocks PASS ≠ G6).

## Resultado

**PASS / gate-done T5.4** 2026-09-14. Hijo cambiar horario **gate-done** 2026-09-14. T6 padre **diferido**. Equipo en [`turnos-agenda-multiples-equipo`](../turnos-agenda-multiples-equipo/).
