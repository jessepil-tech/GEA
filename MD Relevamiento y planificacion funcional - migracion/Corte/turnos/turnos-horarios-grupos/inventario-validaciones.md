---
title: Inventario validaciones T3 — MessageBundle / BB / ImpBus
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-horarios-grupos.validaciones
---

# Inventario validaciones — T3 (g4-3)

**Plantilla de referencia** para el gate obligatorio en
[`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.4+ (sección Inventario
validaciones). Cada corte con pantalla ABM debe tener una tabla equivalente antes de
marcar G4 / handlers done.

Fuente: `BBHorarioTurno*` · `ImpBusHorarioTurGrp*` · `ImpBusDiaHorarioTurGrp*` ·
`MessageBundle` (HOSPITAL-BUSINESS).  
Web: `horarios-turnos-validation.ts` · API: `TurnosHorariosValidation`.

**Lección:** inventariar **BB + ImpBus** del insert/update del corte (no solo FacesMessage).

| Regla | Mensaje / lógica legacy | UI | API |
|-------|-------------------------|----|-----|
| Campos (*) vacíos | Debe completar todos los campos requeridos. | done | done (grupo nombre) |
| Prestación | Se debe seleccionar una prestación. | done | done |
| Fecha vigencia | Debe ingresar la fecha de vigencia. | done | done |
| Fin ≤ vigencia | La fecha de fin de vigencia debe ser posterior… | done | done |
| Solape de vigencias | Existen fechas solapadas. (`ImpBusHorarioTurGrp*`) | done (msg API) | done (+ auto-cierre vigencia abierta) |
| Copiar valores vigentes | Checkbox `msg.copiar_valores_vigentes` → copia días de vigencia anterior | done | done (`?copiarValoresVigentes=true`) |
| Hora desde | Debe ingresar hora y minutos Desde | done | done |
| Hasta ≤ desde | El horario hasta debe ser posterior… | done | done |
| Solape horas mismo día | Existen horarios solapados. (`ImpBusDia*`) | done (msg API) | done |
| Reserva min (si activa) | Debe ingresar la cantidad de minutos… | done (pers) | done (pers) |
| Reserva hs (si activa) | Deber ingresar la cantidad de horas libera | done (pers) | done (pers) |
| Inicio/fin reserva | Debe ingresar si la reserva… | **done** (combo INICIO/FINAL) | done |
| Generar agenda | T4 | diferido | — |

**Feedback UI:** validaciones / `BusinessException` → **toast** (`NotificationsService`),
paridad MessageManager (WARN→`toastWarning`, INFO solape horas→`toastInfo`). No banner
rojo de página para esos casos.

Leyenda UI: `Los campos marcados con (*) son obligatorios`.

## Checklist ImpBus del corte (anti-omisión)

Antes de G4/API done del ABM in-scope:

1. Listar `ImpBus*` tocados por insert/update de la cadena.
2. Extraer cada `BusinessException` / side-effect (auto-cierre, copia hijos).
3. Fila en esta tabla: done / diferido(slug) — **sin silencio**.
