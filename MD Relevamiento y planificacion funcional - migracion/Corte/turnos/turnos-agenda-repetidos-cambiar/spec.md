---
title: Spec — cambiar horario turnos repetidos
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.turnos-agenda-repetidos-cambiar
---

# Spec — Cambiar horario (repetidos)

Padre: [`turnos-agenda-repetidos/`](../turnos-agenda-repetidos/).  
Ruta: **`/turnos/agenda`**.  
Clarify **FIRME** Camino 1 — 2026-09-11 (Francisco «ok firme»). D-TUR-53 · D-TUR-54.

Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

## Problema

T5.3 deja el icono **Cambiar Horario** disabled (⇄) en las tablas Turnos Reservados y Observaciones. HIS abre `$popupCambiarTurno`: grilla de slots `LIBRES` del **mismo día** y al elegir fila reemplaza (reservado) o completa (observación).

## Resultado (objetivo Camino 1)

Misma vista repetidos. Icono habilita el popup 1200×550. Click en un LIBRE: reserva T5 + actualiza las tablas. Cancelar / X cierra sin cambio.

## Clarify — **FIRME** Camino 1 (2026-09-11)

| # | Pregunta | Respuesta | Evidencia |
|---|---------|-----------|-----------|
| 1 | ¿Pipeline? | T5.3 **gate-done** (vista + reserva N). T5 GET grilla `filtroEstado=LIBRES` + POST `…/reservar` + POST `…/liberar`. Paciente/prestación/convenio de north. Oferta T4. Sin tabla nueva. | T5 · T5.3 · `BBTurnosRepetidos` L192–323 |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda` en vista repetidos. No tile. No `turnosMultiples.xhtml`. | `turnosRepetidos.xhtml` L161–183 · L311 |
| 3 | ¿Happy path? | Icono ⇄ en fila **reservada** → popup header `Otros turnos disponibles` → tabla LIBRE del día de esa fila (`00:00`–`23:59`, mismo centro/servicio/personal/prestación) → **rowSelect** reserva el elegido y **reemplaza** la fila. Icono en **observación** → misma grilla del `fechaHoraIniTurno` de la obs → reserva → **agrega** a reservados y **quita** la obs. | BB `actBtnCambiarTurno` · `actBtnCambiarTurnoNoEncontrado` · `actionBtnReemplazarTurno` · xhtml L353–359 |
| 4 | ¿Ciclo de vida? | El acto es **sesión de reserva** (antes de Asignar Turnos / Volver T5.3). HIS reserva el nuevo y pisa el objeto en memoria; **no llama liberar** del id anterior (L268–280). **Camino 1:** reserva el nuevo **y libera el anterior** (si falla la reserva, no se toca el viejo). Volver T5.3 sigue liberando la lista vigente. Cancelar / X: no reserva ni libera. Calendario mes del xhtml está **comentado** → **WAIVE** (HIS vivo = solo ese día). | BB L245–288 · xhtml L319–352 |
| 5 | ¿Errores? | Tabla vacía → `no_se_encontraron_registros`. POST T5 4xx → toast sin `/500`. Sin popup extra (HIS usa `MessageManager`). | xhtml L353 · MessageBundle |
| 6 | ¿Fuera? | Calendario / mes anterior-siguiente (comentado). Múltiples (`turnosMultiples.xhtml` `$popupCambiarTurno`). Pre-agenda. Equipo D-TUR-17. T7. HOS-APP. Endpoint nuevo. | xhtml L319–352 · `turnosMultiples.xhtml` L540 |
| 7 | ¿Gate UI? | Inventarios **antes** de habilitar el icono y el dialog. `$popupCambiarTurno`: `width="1200"` `height="550"` (**cuerpo**; titlebar aparte); `closable=true`; header `Otros turnos disponibles`; tabla `scrollHeight="461"` cols hora **60** / duración **60** / centro / servicio / profesional; header tabla `Turnos {dd/MM/yyyy}`; footer solo **Cancelar** (MarAuto HIS). Look Origin: `app-ds-dialog` + `[dsDialogFooter]` gap 32px, Cancelar al final. CTA = click de fila (no botón Aceptar). Icono ⇄ ya stub T5.3 → habilitar. | `regla-paridad-ui-legacy` v1.14 · DESIGN_SYSTEM modal |
| 8 | ¿Playwright? | **e2e-migrado:** icono reservado → popup → click LIBRE → fila muestra nuevo horario. **e2e-migrado** (si fixture parcial T5.3): icono obs → click LIBRE → obs sale y entra a reservados. Cancelar no cambia. Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API? | Reusar GET `/api/v1/turnos/agenda/grilla?filtroEstado=LIBRES` + POST `…/reservar` + POST `…/liberar` (viejo, solo camino reservado). Sin Resource nuevo. Use-case Web orquesta (como otorga N T5.3). Resource delgado. | scaffold T5 |

**Camino 1** = iconos + popup mismo día + rowSelect reserva + **liberar el slot anterior** (desviación explícita vs BB).  
No hay Camino “solo abrir popup”: HIS reserva en el click de fila.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Icono cambiar en reservados | xhtml L161–163 | **In scope** | No |
| Icono cambiar en observaciones | xhtml L180–182 | **In scope** | No |
| Popup 1200×550 LIBRES del día | L311–376 · `selectGrillaTurnosDia` LIBRES | **In scope** | No (GET T5) |
| rowSelect reserva + reemplaza fila | `actionBtnReemplazarTurno` L268–280 | **In scope** | UPDATE `RESERVADO` (POST T5) |
| Liberar slot anterior | HIS **no** lo hace | **In scope** (Camino 1) | UPDATE/DELETE T5 |
| rowSelect desde obs → suma reservado | L282–285 | **In scope** | UPDATE `RESERVADO` |
| Cancelar / X | L245–247 · `closable=true` | **In scope** | No |
| Calendario cambiar día | xhtml **comentado** L319–352 | **WAIVE** | — |
| Cambiar horario en múltiples | `turnosMultiples.xhtml` | **diferido** `turnos-agenda-multiples` | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Icono ⇄ enabled en ambas tablas de la vista repetidos. |
| RF-2 | Popup 1200×550 con LIBRES del día de la fila/obs; header + cols HIS. |
| RF-3 | Click fila: reserva T5; si venía de reservado, libera el id anterior y pisa la fila; si venía de obs, agrega y quita la obs. |
| RF-4 | Cancelar / X no escribe. |
| RF-5 | Gate UI xhtml **antes** de template del dialog. |
| RF-6 | e2e reemplazo desde reservado. |
| NFR-1 | Sin endpoint nuevo. Schema `ts`. Look Origin / geometría HIS. |

## Decisiones **FIRME**

| Id | Decisión |
|----|----------|
| D-TUR-53 | Popup en `/turnos/agenda` (vista repetidos). Lectura = GET grilla T5 `LIBRES` del mismo día. Escritura = POST reservar T5. |
| D-TUR-54 | Reemplazo = reserva nuevo **luego** libera el anterior (si la reserva falla, no se toca el viejo). Calendario HIS comentado = **WAIVE**. |
