---
title: Spec — T6.5 · reemplazo profesional de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo
---

# Spec — Reemplazo profesional de grilla

Padre T6 `turnos-ciclo-vida` (resto **diferido**: vencidos).  
T4 [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) ya cobró generar / eliminar / consulta del mismo menú `agenda_turnos`.  
T6.4 [`turnos-grilla-suspender/`](../turnos-grilla-suspender/) cobró suspender / quitar.  
Clarify **FIRME Camino 1** — 2026-09-21 (Francisco). D-TUR-74.

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md).

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto, `cliente="TS"`) |
| Ramas `esClienteX()` | ninguna en `BBReemplazoPersonalGrillaTurnos` |
| Decisión | camino genérico · resto `diferido(multi-instalacion)` |

## Problema

T4 materializa oferta `LIBRE`. El menú HIS **Reemplazar profesional** (10824) sigue sin portar: los slots no marcan `personal_reemplazado='S'` ni `id_personal_reemplazo`. T6.1–T6.4 no cubren esta pantalla (`atencionTurno/reemplazoPersonalGrillaTurnos.xhtml`).

## Resultado (objetivo Camino 1)

Una hoja del menú TURNOS / Agenda turnos (chrome T4):

1. Buscar **profesional origen** (`buscadorPersonalServicio`, reuso T2/T4) → centro/servicio **disabled** desde el vínculo.
2. Rango fechas (`mindate` = hoy) + horas 00:00–23:59 + días + feriado → **Consultar** lista `f_get_grilla_personal_fechas`.
3. Buscar **profesional reemplazante** (mismo buscador; filtro `idServicio` del origen) + combo motivo `tipo_motivo=REEMPLAZO_TURNO`.
4. Seleccionar filas → **Reemplazar Agenda** (confirm) → `f_reemplazar_personal` (ids + motivo + horas).
5. **Quitar Reemplazo** (confirm) → el mismo SP con personal/motivo **null** (ignora hora; limpia todo el hueco).

Port Java + golden master (como T4/T6.4). Sin GTT `tmp_turno` en runtime: el comando lleva ids. Este SP **no** escribe mail/SMS.

## Clarify — **FIRME Camino 1** (2026-09-21)

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T2/T3/T4 **gate-done**. Motivo GET T1 (`REEMPLAZO_TURNO`). Call center T1/T5. Reemplazante HAB en el mismo servicio. | verify padres |
| 2 | ¿Misma familia UI? | **Sí** menú `agenda_turnos` id **10824** junto a Generar/Eliminar/Suspender. **NO** `/turnos/agenda`. Ruta `/configuracion/grilla-turnos-reemplazo` (URL Configuración; **menú** TURNOS, como T4). | dump 10824 · T4 rutas |
| 3 | ¿Happy path? | Consultar → check filas → Reemplazar (modal copy `¿Desea reemplazar el profesional?`) → slots `id_personal_reemplazo` + `id_motivo_reemplazo` + `personal_reemplazado='S'` + `pf_hist_turno`. Quitar: mismo flujo, nulos + `'N'`. Hueco completo vs horas north: split igual T4/T6.4. | BB `actionBtnReemplazarPersonal` / `actionBtnQuitarReemplazo` · BODY 11657–11845 |
| 4 | ¿Ciclo de vida? | Reemplazo **no** cambia `estado_turno`. Split INSERT+UPDATE si las horas recortan un `LIBRE`. Parcial sobre otorgado (`id_paciente` not null) → error contrato. Quitar ignora hora. Escritores cruzados RECEPCIONES `f_reemplazar_personal_cola` **fuera**. | BODY 11692–11838 |
| 5 | ¿Errores / permisos? | BB: `Debe ingresar el Personal` / `Debe ingresar el personal reemplazante` / `Debe ingresar el motivo de reemplazo` / `Debe seleccionar al menos un turno` / `WRONG_INTERVAL_DATE_3` / `WRONG_INTERVAL_HOUR`. SP: `Existen turnos solapados para este personal.` / `No se puede reemplazar parcialmente el personal de un turno otorgado.` Toast éxito: `Reemplazo realizado exitosamente` (constante `REEMPLAZO_RELIAZADO_*` = rareza de nombre; copy HIS). Perfil menú `ATENCION_TURNOS` / hoja 10824. Rol funcional: **ninguno** en este bean. Actor **sin** entrada de menú: no entra. PASS admin no cuenta. | MessageBundle · BODY 11684 · 11709 |
| 6 | ¿Side-effects? | **Portar** `f_reemplazar_personal` (golden master). Invoca `pf_hist_turno` (hist in-scope). **No** mail/SMS en este SP. Jobs: `MigrarTurnoVencidoJob` **N/A** este xhtml (`diferido` hijo vencidos); `MailTurnoJob` **T7**; `CheckHabTurnosJob` **N/A** (T2). Sin sidecar. | BODY 11838 · `--jobs turno` |
| 7 | ¿Paridad UI / Gate? | Inventarios G0 **antes** de template. Buscadores reuso T2/T4 (`pages/buscadores` tronco). Consultar **D-TUR-71** fila propia 150px (HIS lo deja inline con días). Footer `dataTable` scrollable + facet → leyenda **anclada** v1.19 (`gt-his-consulta-leyenda`; no `obsTableWrap` T4). Confirm HIS `window.confirm` → modal DS (canon feedback; copy HIS). Look Origin. | `reemplazoPersonalGrillaTurnos.xhtml` · Gate UI · v1.19 |
| 8 | ¿Viaje Playwright? | **e2e-migrado:** (a) menú abre reemplazo; (b) Consultar `LIBRE` fixture T4; (c) reemplazar con motivo → `personal_reemplazado='S'`; (d) quitar → nulos. Legacy HIS **no**. | `regla-playwright-migracion.md` |
| 9 | ¿Fuera? | Vencidos job. T7 avisos. Equipo D-TUR-17 (N/A esta hoja). RECEPCIONES cola. ATENCION. HOS-APP. T6.4 suspender. Accordion Agenda. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Solo reemplazar, quitar después | Techo **ok** (3/8); HIS ya tiene los dos botones en la misma hoja |
| T6 padre entero (reemplazo + vencidos) | Techo de naturaleza distinta: pantalla vs job que cruza cola/anunciador/T7 |
| Meterlo en `/turnos/agenda` | HIS es `atencionTurno`, no accordion |
| Escribir `tmp_turno` | T4 ya rediseñó a lista de ids |
| Combo Equipo | Esta hoja **no** lo tiene; D-TUR-17 sigue en otras |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Reemplazar profesional menú | `reemplazoPersonalGrillaTurnos.xhtml` · 10824 | **In scope** | **Sí** |
| Quitar reemplazo (misma hoja) | `actionBtnQuitarReemplazo` | **In scope** | **Sí** |
| Consultar grilla personal | `f_get_grilla_personal_fechas` | **In scope** (JDBC equivalente, **rediseñar**) | No |
| Buscador profesional ×2 | `buscadorPersonalServicio.xhtml` | **In scope** (reuso T2/T4, no re-portar) | No |
| Split hueco parcial LIBRE | BODY elsif | **In scope** | **Sí** INSERT `turno` |
| Solape reemplazante | BODY count | **In scope** | No (abort) |
| Motivo `REEMPLAZO_TURNO` | `Constants.STRING_REEMPLAZO_TURNO` | **In scope** (GET T1) | No (elige id) |
| Equipo | — | **N/A** esta hoja | — |
| Mail/SMS | — | **N/A** este SP | — |
| Vencidos | `MigrarTurnoVencidoJob` | **N/A** este xhtml → `diferido` hijo | — |
| Check hab job | `CheckHabTurnosJob` | **N/A** (T2 / D-TUR-11) | — |
| `f_reemplazar_personal_cola` | package RECEPCIONES | **N/A** este xhtml | — |
| HOS-APP | — | **N/A** | — |

## Firmas PL/SQL

| Firma | Decisión | Nota |
|-------|----------|------|
| `TURNOS.f_reemplazar_personal` | **portar** (golden master) | GTT → ids; quitar = nulls |
| `TURNOS.f_get_grilla_personal_fechas` | **rediseñar** JDBC | igual T5.5 / T6.4 lista |
| `TURNOS.pf_hist_turno` | **portar** (invocada; reusar T5/T6.4) | INSERT `hist_turno` |
| `GENERAL.f_next_id_tabla` | **puente** existente NextId | no clonar BODY GENERAL · split |

Casos golden mínimos: NULL reemplazante (quitar), lista vacía, cero ids, motivo null, borde de hora (parcial inicio/fin/medio), `OTORGADO` parcial, solape, NULL/vacío/cero fecha.

## Acceso y trazabilidad

Perfiles: entrada menú TURNOS / `agenda_turnos` (`reemplazar_profesional` 10824).  
Rol funcional PL/SQL: **ninguno** en este bean (no `Raise_application_error` de `rol_funcional_pers`).  
Prueba negativa: actor **sin** esa hoja de menú.  
Auditoría: `ts.AUD_TURNO` existe. Este corte **no** porta `TBL_AUD_*` → **`diferido(auditoria)`**. Riesgo: reemplazo/split en `turno` sin valor viejo/nuevo campo a campo. `fecha_last_update` / `actualizado_por` = último cambio, no historial. `hist_turno` sí se escribe (`pf_hist_turno`).

## Presupuesto no funcional (paso 3)

| Eje | Presupuesto | Cómo se mide |
|-----|-------------|--------------|
| Tiempo | p95 POST reemplazar ≤ 1,5 s (mismo orden T4 eliminar / T6.4) | N repeticiones; **percentil**, no promedio |
| Volumen | dataset del orden del dump `ts.turno`; si no hay masa → «medido en vacío» + `diferido(perf-volumen)` | nunca PASS solo con 1 fila |
| Concurrencia | dos actores sobre el mismo `id_turno` | SP **sin** `FOR UPDATE` en el loop leído; last-write o error de solape observable; documentar. CU multi-tabla (turno + hist + split) → evidencia de **rollback** si falla a mitad |

## Decisiones

| ID | Decisión |
|----|----------|
| D-TUR-74 | Camino 1 **FIRME**: una hoja reemplazar **y** quitar; menú T4; port SP; GTT→ids; vencidos fuera; T7 N/A este SP; no T6 padre entero. |

## Fuera de alcance (no silencio)

| Capacidad | Destino |
|-----------|---------|
| Vencidos job | [`turnos-ciclo-vida/`](../turnos-ciclo-vida/) |
| Mail/SMS/WhatsApp | T7 avisos (N/A este SP; MailTurnoJob sigue T7) |
| Equipo usable | D-TUR-17 (otras hojas) |
| `f_reemplazar_personal_cola` | circuito recepción |
| Suspender / quitar | T6.4 ya cobrado |
| Accordion Agenda | T5–T6.3 ya cobrados |
