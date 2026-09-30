---
title: Verify — cambiar horario turnos múltiples
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar.verify
---

# Verify

**Gate:** **PASS / gate-done** 2026-09-14 · Clarify **FIRME** Camino 1. D-TUR-57 · D-TUR-58.  
G6 smoke ops **PASS** (Francisco: «si funciona»).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Icono cambiar en slots | **done** | xhtml L301 |
| Popup LIBRES mismo día | **done** | `$popupCambiarTurno` |
| rowSelect swap in-memory | **done** | BB `actionBtnReemplazarTurnoMultiple` |
| Cancelar / X | **done** | BB L2746 |
| Recorte ventana vecinos | **WAIVE** | BB L2671–2685 no usado |
| Reserva/libera en el click | **WAIVE** | HIS no escribe |
| Equipo en GET | **diferido** | D-TUR-17 |
| Cambiar en repetidos | **done** | T5.3-b |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | vista múltiples → ⇄ slot → popup → click LIBRE → fila con nueva hora |
| Viaje 2 | Cancelar / X no cambia filas |
| Fixture | mocks `turnos-agenda.spec.ts` (GET grilla `LIBRES` + hueco 23:40) |
| Legacy e2e | **no** |

E2E mocks ≠ G6. Universo de CU = esta matriz. e2e **PASS** 2026-09-14.

## Paridad UI (xhtml)

Fuente: `turnosMultiples.xhtml` L301–303 · L540–575.  
Inventarios G0: **hecho** 2026-09-14.

| Control | Estado | Nota |
|---------|--------|------|
| Inventario msg.* ↔ labels | **done** | `otrosTurnosDisponibles` · `cambiarHorario` |
| Inventario validaciones | **done** | toast MessageBundle; GET 4xx sin `/500` |
| Inventario interacción | **done** | ⇄ + rowSelect + Cancelar |
| Dialog 1200×550 | **done** | `app-ds-dialog` `!max-w-[1200px]` · cuerpo 550 |
| Tabla LIBRES hora/duración leíble | **done** | CSS T5.3-b 5rem/6rem (HIS 52/20) |

## Smoke

**G6 stack real — PASS 2026-09-14** (ops Francisco, `/turnos/agenda` vista múltiples, «si funciona»):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | ⇄ slot → popup Otros turnos disponibles → click LIBRE → fila nueva hora (sin POST hasta Asignar) | **PASS** |
| 2 | Cancelar / X no cambia | **PASS** (Cubierto e2e; ops confirmó el acto) |

## Resultado

**PASS / gate-done T5.4-b** 2026-09-14. Cola corta T5.4 **cobrada**. T6 padre (`turnos-ciclo-vida`) **diferido**. D-TUR-17 equipo.
