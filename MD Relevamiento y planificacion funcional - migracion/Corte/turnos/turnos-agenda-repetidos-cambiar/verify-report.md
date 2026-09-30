---
title: Verify — cambiar horario turnos repetidos
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar.verify
---

# Verify

**Gate:** **PASS / gate-done** 2026-09-11 · Clarify **FIRME** Camino 1. D-TUR-53 · D-TUR-54.  
G6 smoke ops **PASS** (Francisco: «joya se ve bien»).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Icono cambiar reservados / obs | **done** | xhtml L161 · L180 |
| Popup LIBRES mismo día | **done** | `$popupCambiarTurno` |
| rowSelect reserva + reemplaza / completa obs | **done** | BB `actionBtnReemplazarTurno` |
| Liberar slot anterior | **done** (Camino 1; HIS no lo hace) | D-TUR-54 · motivo `91004` |
| Cancelar / X | **done** | BB L245 |
| Calendario cambiar día | **WAIVE** | xhtml L319–352 comentado |
| Cambiar en múltiples | **diferido** | `turnos-agenda-multiples` |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | vista repetidos → ⇄ reservado → popup → click LIBRE → fila con nueva hora |
| Viaje 2 | ⇄ obs → click LIBRE → obs sale / entra reservados |
| Viaje 3 | Cancelar no cambia filas |
| Fixture | mocks `turnos-agenda.spec.ts` |
| Legacy e2e | **no** |

E2E mocks ≠ G6. Universo de CU = esta matriz. e2e **PASS** 2026-09-11.

## Paridad UI (xhtml)

Fuente: `turnosRepetidos.xhtml` L161–183 · L311–382.  
Inventarios G0: **hecho** 2026-09-11.

| Control | Estado | Nota |
|---------|--------|------|
| Inventario msg.* ↔ labels | **done** | `otrosTurnosDisponibles` · `cambiarHorario` · `duracion` |
| Inventario validaciones | **done** | toast 4xx sin `/500` |
| Inventario interacción | **done** | ⇄ + rowSelect + Cancelar |
| Dialog 1200×550 | **done** | `app-ds-dialog` `!max-w-[1200px]` · cuerpo 550 |
| Tabla LIBRES hora/duración leíble | **done G6** | cols 5rem / 6rem (HIS 60px era PF padding 2px) |
| Vista tablas sin scroll horizontal | **done G6** | `table-layout: fixed` · col Acciones 6rem |
| Prestación north nombre + código | **done G6** | flex 1 + 5.5rem (no `w-full` DS) |

## Smoke

**G6 stack real — PASS 2026-09-11** (ops Francisco, `/turnos/agenda` vista repetidos, «joya se ve bien»):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | ⇄ reservados / observaciones → popup Otros turnos disponibles | **PASS** |
| 2 | Visual: ficha prestación visible; tablas sin scroll inicial; Acciones entero; modal hora/headers | **PASS** |

## Resultado

**PASS / gate-done T5.3-b** 2026-09-11. Cola corta T5.3 **cobrada**. T6 padre (`turnos-ciclo-vida`) **diferido**. D-TUR-17 equipo. Múltiples diferido.
