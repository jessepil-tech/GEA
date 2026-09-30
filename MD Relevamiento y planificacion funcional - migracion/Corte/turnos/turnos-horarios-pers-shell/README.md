---
title: SDD — T3 hijo · shell UI Turnos por Profesional
description: Paridad west + inicio/fin reserva (gap post gate T3).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-horarios-pers-shell
---

# turnos-horarios-pers-shell

**Estado:** **gate-done** (implement 2026-09-01) · west + inicio/fin reserva.  
**Padre:** [`turnos-horarios-grupos/`](../turnos-horarios-grupos/) (T3).

## Problema

T3 marcó UI pers **done**, pero Web usaba **tabs** en lugar del **menú west** legacy
(`turnosPersonal.xhtml`) — a diferencia de Turnos por Servicio. Faltaba combo
**inicio/fin** reserva (`reservaInicioFinalTur`).

## Hecho

| Ítem | Evidencia |
|------|-----------|
| Shell north-west-center | `horarios-turnos-pers.component.ts` — `Sz.shell` / `navWest` |
| Secciones | Datos Personales · Grupo · Prestaciones · Horario Trabajo |
| Diferidos west (disabled) | Horario especial / Inhibición / Ocupaciones |
| Combo INICIO/FINAL + validación | Días form + grid; WARN si min/hs > 0 sin valor |
| Labels | `datosPersonales`, `inicio`, `final`, `inicioFin` |

## No objetivos

T4; inhibiciones/especiales ABM; paginator grillas (polish).

## Verify

UI pers: sin tabs; west como serv; guardar día con reserva exige inicio/fin.

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **cerrado-pre-regla** |
| Viaje (pasos) | implement + gate padre T3 2026-09-01 |
| Fixture | — |
| Legacy e2e | no |
