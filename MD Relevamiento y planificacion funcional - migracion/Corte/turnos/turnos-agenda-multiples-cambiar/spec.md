---
title: Spec — cambiar horario turnos múltiples
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-multiples-cambiar
---

# Spec — Cambiar horario (múltiples)

Padre: [`turnos-agenda-multiples/`](../turnos-agenda-multiples/).  
Ruta: **`/turnos/agenda`**.  
Clarify **FIRME** Camino 1 — 2026-09-14 (Francisco «ok»). D-TUR-57 · D-TUR-58.

Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

## Problema

T5.4 deja el icono **Cambiar Horario** disabled (⇄) en la tabla de slots. HIS abre `$popupCambiarTurno`: grilla `LIBRES` del **mismo día** para la prestación/centro/servicio/profesional de esa fila y al elegir fila **pisa el slot en memoria**. La escritura es **Asignar Turno** del padre.

## Resultado (objetivo Camino 1)

Misma vista múltiples. Icono habilita el popup 1200×550. Click en un LIBRE: reemplaza hora/id/centro/servicio/profesional de esa fila. Cancelar / X cierra sin cambio. **No POST** hasta Asignar.

## Clarify — **FIRME** Camino 1 (2026-09-14)

| # | Pregunta | Respuesta | Evidencia |
|---|---------|-----------|-----------|
| 1 | ¿Pipeline? | T5.4 **gate-done** (carrito + consultar + reserva lote). North paciente/convenio T5.1. Oferta T4. GET grilla T5 `filtroEstado=LIBRES`. Sin tabla nueva. Sin `tmp_turno*`. | T5 · T5.4 · `BBAgenda` L2639–2749 · SP `f_get_grilla_dia` |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda` en vista múltiples. No tile. No `turnosRepetidos.xhtml`. | `turnosMultiples.xhtml` L301–303 · L540 |
| 3 | ¿Happy path? | Icono ⇄ en fila de slots → popup header `Otros turnos disponibles` → tabla LIBRE del día de esa fila (`00:00`–`23:59`, prestación/centro/servicio/personal de la fila; si el filtro no traía centro, HIS usa el del slot) → **rowSelect** pisa `idTurno`, ini/fin, duración, centro, servicio, personal **en la fila** y cierra. CTA = click de fila (no Aceptar). | BB `actBtnCambiarTurnoMultiple` · `actionBtnReemplazarTurnoMultiple` · xhtml L547–551 |
| 4 | ¿Ciclo de vida? | El acto es **sesión de combo** (antes de Asignar Turno T5.4). HIS **no reserva ni libera**. Camino 1 = igual: solo memoria. Asignar del padre POST `reservar-multiples` con los ids/ini/fin vigentes. Volver / Limpiar: sin escritura. Cancelar / X: no pisa la fila. BB calcula ventana entre vecinos del combo (L2671–2685) y **no la usa** (`desde`/`hasta` fijos `00:00`/`23:59`) → **WAIVE** el recorte (paridad HIS vivo). | BB L2727–2748 · L2683–2691 |
| 5 | ¿Errores? | Tabla vacía → `no_se_encontraron_registros`. Filtro no resuelto → `Error`. Límites `cantidadMaxTurnoExcedida` / no modificable / hora ≤ sysdate → MessageBundle toast. GET T5 4xx → toast sin `/500`. Sin popup extra. | BB L2699–2726 · xhtml L547 |
| 6 | ¿Fuera? | Reserva/libera en el click (eso es T5.3-b). Equipo usable **D-TUR-17**. Pre-agenda. T7. HOS-APP. Endpoint nuevo. Calendario de mes (este popup **no** lo tiene; el de repetidos está comentado). | xhtml L540–575 · T5.3-b |
| 7 | ¿Gate UI? | Inventarios **antes** de habilitar el icono y el dialog. `$popupCambiarTurno`: `width="1200"` `height="550"` (**cuerpo**; titlebar aparte); `closable=true`; header `Otros turnos disponibles`; tabla `scrollHeight="465"` cols hora **52** / duración **20** / centro / servicio / profesional (**sin** prestación); header tabla `Turnos {dd/MM/yyyy}`; footer solo **Cancelar**. Look Origin: reusar CSS `gt-his-cambiar-*` (hora/duración leíbles 5rem/6rem como T5.3-b; HIS 52/20 era PF). CTA = click de fila. Icono ⇄ stub T5.4 → habilitar. | `regla-paridad-ui-legacy` v1.14 · DESIGN_SYSTEM modal |
| 8 | ¿Playwright? | **e2e-migrado:** icono slot → popup → click LIBRE → fila muestra nuevo horario. **e2e-migrado:** Cancelar / X no cambia. Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API? | Reusar GET `/api/v1/turnos/agenda/grilla?filtroEstado=LIBRES` (mismo T5.3-b) con ids de la fila. **Sin** POST reservar/liberar en este acto. **Sin** Resource nuevo. Use-case Web orquesta el swap. Resource delgado. | scaffold T5 |

**Camino 1** = icono + popup mismo día + rowSelect **swap in-memory**.  
No hay Camino “POST al elegir fila”: HIS no escribe hasta Asignar.

HIS marca el filtro en `tmp_turno.id_motivo_suspende = tmp_turno_filtro.id_objeto`. Web no tiene `tmp_*`: la fila ya trae prestación/centro/servicio/personal del empaquetado T5.4 → esas keys alimentan el GET (si el filtro no tenía centro, el slot sí).

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Icono cambiar en slots | xhtml L301–303 | **In scope** | No |
| Popup 1200×550 LIBRES del día | L540–575 · `selectGrillaTurnosDia` LIBRES | **In scope** | No (GET T5) |
| rowSelect pisa la fila | `actionBtnReemplazarTurnoMultiple` L2727–2739 | **In scope** | No |
| Cancelar / X | L2746–2748 · `closable=true` | **In scope** | No |
| Recorte ventana vecinos | BB L2671–2685 **no usado** | **WAIVE** | — |
| Reserva/libera en el click | HIS **no** | **WAIVE** (evidencia: solo setters) | — |
| Cambiar horario en repetidos | `turnosRepetidos.xhtml` | **done** T5.3-b | — |
| Equipo en el GET | `filto.codItemEquipo` | **diferido** D-TUR-17 | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Icono ⇄ enabled en la tabla de slots múltiples. |
| RF-2 | Popup 1200×550 con LIBRES del día de la fila; header + cols HIS (sin prestación). |
| RF-3 | Click fila: pisa el slot en memoria; cierra el popup. No POST. |
| RF-4 | Cancelar / X no cambia la tabla. |
| RF-5 | Gate UI xhtml **antes** de template del dialog. |
| RF-6 | e2e reemplazo de hora + Cancelar. |
| NFR-1 | Sin endpoint nuevo. Schema `ts`. Look Origin / geometría HIS. |

## Decisiones **FIRME**

| Id | Decisión |
|----|----------|
| D-TUR-57 | Popup en `/turnos/agenda` (vista múltiples). Lectura = GET grilla T5 `LIBRES` del mismo día con keys de la fila. Escritura = ninguna hasta Asignar T5.4. |
| D-TUR-58 | Reemplazo = swap in-memory (paridad BB). Ventana vecinos no usada en HIS vivo = **WAIVE**. Equipo fuera (D-TUR-17). |
