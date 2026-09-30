---
title: Spec — T6.4 · suspender / quitar suspensión de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender
---

# Spec — Suspender / quitar suspensión de grilla

Padre T6 `turnos-ciclo-vida` (resto **diferido**: reemplazo / vencidos).  
T4 [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) ya cobró generar / eliminar / consulta del mismo menú `agenda_turnos` (10820 / 10823 / 10825).  
Clarify **FIRME Camino 1** — 2026-09-18 (Francisco). D-TUR-72.

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md).

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto, `cliente="TS"`) |
| Ramas `esClienteX()` | ninguna en `BBSuspenderGrillaTurnos` / `BBQuitarCancelacionGrillaTurnos` |
| Decisión | camino genérico · resto `diferido(multi-instalacion)` |

## Problema

T4 materializa oferta `LIBRE`. El menú HIS **Suspender agenda** / **Quitar suspensión** sigue sin portar: slots no pasan a `SUSPENDIDO` ni vuelven a `LIBRE`. T6.1–T6.3 no cubren estas pantallas (`atencionTurno/*`, no el accordion del Turnero).

## Resultado (objetivo Camino 1)

Dos hojas del menú TURNOS / Agenda turnos (chrome T4):

1. **Suspender:** radio serv/pers (**equipo disabled** D-TUR-17) → Consultar lista → seleccionar → popup motivo → `f_suspender_turnos`.
2. **Quitar suspensión:** mismos filtros → lista `SUSPENDIDO` → `f_quitar_suspension` (estado `LIBRE`, limpia motivo/fecha/personal suspende).

Port Java + golden master (como T4). Sin GTT `tmp_turno` en runtime: el comando lleva ids (paridad T4 eliminar). Mail/SMS/WA del SP → **T7**.

## Clarify — **FIRME Camino 1** (2026-09-18)

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T2/T3/T4 **gate-done**. Motivo GET T1. Call center T1/T5. | verify padres |
| 2 | ¿Misma familia UI? | **Sí** menú `agenda_turnos` junto a Generar/Eliminar. **NO** `/turnos/agenda`. Rutas `/configuracion/grilla-turnos-suspender` y `…-quitar-suspension` (URL Configuración; **menú** TURNOS, como T4). | dump 10821 / 10822 · T4 rutas |
| 3 | ¿Happy path? | Suspender: Consultar → check filas → Suspender → popup motivo → Aceptar → slots `SUSPENDIDO` + `id_motivo_suspende` / `fecha_suspende` / `id_personal_suspende`. Si el slot tiene paciente: `pp_libera_turno` + INSERT `turno_a_reasignar` (si `fecha_sol_turno` not null) + hist. Quitar: Consultar SUSPENDIDO → Quitar → `LIBRE` y nulos de suspensión. | BB `actionBtnAceptarMotivo` · BODY 11240–11245 · 11504–11509 |
| 4 | ¿Ciclo de vida? | Hueco completo: UPDATE estado. Hueco parcial (solo `LIBRE`): **split** INSERT + UPDATE (igual eliminar T4). Parcial sobre otorgado → error contrato. Escritores cruzados ATENCION (`f_suspender_turnos` al confirmar) **fuera**. | BODY 11246–11251 · ATENCION ~16742 |
| 5 | ¿Errores / permisos? | SP: `Debe seleccionar algun turno a cancelar.` / `Debe seleccionar el motivo de cancelacion.` / `No se puede suspender parcialmente un turno otorgado.` (**rareza:** dice cancelar; contrato, no corregir). BB: `EQUIPO_REQUIRED_ERROR` solo modo equipo → N/A D-TUR-17. Perfil menú `ATENCION_TURNOS` / hojas 10821–10822. Rol funcional: **ninguno** en estos beans. Actor **sin** entrada de menú: no entra. PASS admin no cuenta. | BODY 11164–11169 · 11250 · MessageBundle |
| 6 | ¿Side-effects? | **Portar** `f_suspender_turnos` y `f_quitar_suspension` (golden master). Invocan `pp_libera_turno` / `pf_hist_turno` (hist in-scope). `p_genera_mail_cancelacion` + `envio_sms` + `mensaje_whatsapp` → **T7**. Jobs: `MigrarTurnoVencidoJob` **N/A**; `MailTurnoJob` **T7**; `CheckHabTurnosJob` **N/A** (T2). Sin sidecar de reportes. | BODY 11384–11459 · `--jobs turno` |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Radio + buscadores T4 (reuso, no re-portar `pages/buscadores`). Equipo radio **visible disabled**. Popup motivo. Tabla selección. Look Origin. | `suspenderGrillaTurnos.xhtml` · Gate UI |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) menú abre suspender; (b) Consultar `LIBRE` fixture T4; (c) suspender con motivo → fila `SUSPENDIDO`; (d) quitar → `LIBRE`. Legacy HIS **no**. | `regla-playwright-migracion.md` |
| 9 | ¿Fuera? | Equipo D-TUR-17. Reemplazo profesional. Vencidos job. T7 avisos. ATENCION writer. Imagenología `quitarSuspension`. HOS-APP. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Solo suspender, quitar después | Techo **ok** (7/8); el undo es el mismo estado `SUSPENDIDO` |
| T6 padre entero | Techo; T6.1–T6.3 ya partieron |
| Combo Equipo live | D-TUR-17; radio disabled |
| Escribir `tmp_turno` | T4 ya rediseñó a lista de ids |
| Meterlo en `/turnos/agenda` | HIS es `atencionTurno`, no accordion |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Suspender agenda menú | `suspenderGrillaTurnos.xhtml` · 10821 | **In scope** | **Sí** |
| Quitar suspensión menú | `quitarCancelacionGrillaTurnos.xhtml` · 10822 | **In scope** | **Sí** |
| Consultar candidatos | `selectTurnosEntreFechasFiltroPersonalServicio` | **In scope** (JDBC equivalente, **rediseñar**) | No |
| Popup motivo | `$popUpSeleccionMotivo` | **In scope** | No (elige id) |
| Split hueco parcial LIBRE | BODY elsif | **In scope** | **Sí** INSERT `turno` |
| Otorgado + suspender completo | `pp_libera_turno` + cola reasignar | **In scope** | **Sí** |
| Equipo radio/filtro | `TipoFiltroEquipo` | **diferido** D-TUR-17 | — |
| Mail/SMS/WA suspensión | BODY 11385+ · `p_genera_mail_cancelacion` | **diferido** T7 | — |
| Reemplazo profesional | `reemplazoPersonalGrillaTurnos` | **diferido** [`turnos-grilla-reemplazo`](../turnos-grilla-reemplazo/) | — |
| Vencidos | `MigrarTurnoVencidoJob` | **N/A** este xhtml | — |
| Check hab job | `CheckHabTurnosJob` | **N/A** (T2 / D-TUR-11) | — |
| ATENCION llama el SP | package ATENCION | **N/A** este xhtml | — |
| HOS-APP | — | **N/A** | — |

## Firmas PL/SQL

| Firma | Decisión | Nota |
|-------|----------|------|
| `TURNOS.f_suspender_turnos` | **portar** (golden master) | GTT → ids; rareza copy «cancelar» |
| `TURNOS.f_quitar_suspension` | **portar** (golden master) | ídem |
| `TURNOS.pp_libera_turno` | **portar** (invocada; reusar T5 si ya cubre) | hist + libera paciente |
| `TURNOS.pf_hist_turno` | **portar** (invocada) | INSERT `hist_turno` |
| `GENERAL.f_next_id_tabla` / `f_get_id_personal_logeado` | **puente** existente NextId / JWT | no clonar BODY GENERAL |
| `GENERA_MAILS.p_genera_mail_cancelacion` | **diferido** T7 | — |
| SELECT lista (`selectTurnosEntreFechas…`) | **rediseñar** JDBC | no hay `f_get_*` de esta hoja; igual T5.5 |

Casos golden mínimos: NULL motivo, lista vacía, cero ids, borde de hora (parcial inicio/fin), `OTORGADO` parcial.

## Acceso y trazabilidad

Perfiles: entrada menú TURNOS / `agenda_turnos` (`suspender_agenda`, `quitar_suspension`).  
Rol funcional PL/SQL: **ninguno** en estos beans (no `Raise_application_error` de `rol_funcional_pers`).  
Prueba negativa: actor **sin** esas hojas de menú.  
Auditoría: `ts.AUD_TURNO` existe (inventario DDL). Este corte **no** porta `TBL_AUD_*` → **`diferido(auditoria)`**. Riesgo: cambios de estado/split en `turno` sin valor viejo/nuevo campo a campo. `fecha_last_update` / `actualizado_por` = último cambio, no historial. `hist_turno` sí se escribe (`pf_hist_turno`).

## Presupuesto no funcional (paso 3)

| Eje | Presupuesto | Cómo se mide |
|-----|-------------|--------------|
| Tiempo | p95 POST suspender ≤ 1,5 s (mismo orden T4 eliminar) | N repeticiones; **percentil**, no promedio |
| Volumen | dataset del orden del dump `ts.turno`; si no hay masa → «medido en vacío» + `diferido(perf-volumen)` | nunca PASS solo con 1 fila |
| Concurrencia | dos actores sobre el mismo `id_turno` | SP **sin** `FOR UPDATE` en el loop leído; last-write o error observable; documentar. CU multi-tabla (turno + hist + cola) → evidencia de **rollback** si falla a mitad |

## Decisiones

| ID | Decisión |
|----|----------|
| D-TUR-72 | Camino 1: suspender **y** quitar en este slug; menú T4; port SP; GTT→ids; equipo D-TUR-17; T7 avisos; no T6 padre entero. |

## Fuera de alcance (no silencio)

| Capacidad | Destino |
|-----------|---------|
| Equipo usable | D-TUR-17 |
| Reemplazo profesional | [`turnos-grilla-reemplazo`](../turnos-grilla-reemplazo/) |
| Vencidos job | `turnos-ciclo-vida` (hijo) |
| Mail/SMS/WhatsApp | T7 avisos |
| Writer ATENCION | circuito amb/int |
| Accordion Agenda | T5–T6.3 ya cobrados |
