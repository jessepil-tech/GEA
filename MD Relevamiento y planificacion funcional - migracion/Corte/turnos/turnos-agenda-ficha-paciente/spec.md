---
title: Spec — T5.1 Turnos agenda ficha paciente
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-ficha-paciente
---

# Spec — T5.1 Ficha paciente + west agenda

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) · [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · corte **T5 hijo** (west + north paciente).  
[`cortes.md`](../../../relevamiento/relevamiento-turnos/cortes.md) · D-TUR-24 en T5 spec.

## Problema

T5 cerró el núcleo A7 (grilla + reservar → otorgar → liberar) con **ids demo hardcodeados** en north (`idPaciente=20001`, convenio/plan fijos) y west **parcial** (calendario + mini-nav; sin Desde/Hasta, leyenda calendario completa, ficha paciente, tabla otros centros).

Call center no puede operar la agenda como legacy sin **buscar paciente/convenio** y ver **ficha west** sincronizada al seleccionar.

## Resultado (objetivo)

1. **North agenda:** buscador paciente (apellido + dialog) + buscador convenio (dialog) + combo plan + campos afiliado/documento + limpiar paciente — paridad `agenda.xhtml` filas paciente/convenio (sin WS elegibilidad).
2. **West 260px:** Desde/Hasta (time), leyenda 6 colores calendario, panel **Datos paciente** read-only, tabla **Primer turno otros centros** con menú gear (asignar / consultar agenda / información — últimos dos según capacidad v1).
3. **API:** búsqueda paciente/convenio/planes + ficha resumen paciente + query otros centros (lectura JDBC sobre `ts.*` existente V31).
4. **Web:** extender `/turnos/agenda` (no nueva ruta) · use-cases · dialogs reutilizables.
5. Inventarios gate UI (copy + interacción + validaciones) **antes** de template.
6. IT + e2e ampliado + verify anti-gap.

## Clarify — **FIRME** (2026-09-07 · Camino 1)

Decisión producto: **Camino 1** — completar `/turnos/agenda` (T5.1) sin shell 170px ni pantallas hijas nuevas.

| # | Pregunta | Respuesta FIRME | Evidencia / nota |
|---|----------|-----------------|------------------|
| 1 | ¿Misma ruta UI? | **Sí** — `/turnos/agenda`; extiende T5, no fork. | T5 entry C7 |
| 2 | ¿North in-scope? | Buscador **paciente** + **convenio** + **plan** + nro afiliado/doc + limpiar + info búsqueda + info convenio (popup observaciones). | `agenda.xhtml` L28–120 · `asignacionTurnos.xhtml` popups |
| 3 | ¿West in-scope? | Desde/Hasta · leyenda calendario 6 estilos · ficha read-only · otros centros (tabla + gear). | `agenda.xhtml` L313–517 |
| 4 | ¿Accordion Paciente 170px? | **diferido** `turnos-asignacion-shell`. v1: no render accordion. | `asignacionTurnos.xhtml` tab Paciente |
| 5 | ¿Pantalla `datosPaciente.xhtml`? | **diferido** — tabs ABM = slugs propios o shell. v1: ficha west + buscadores north. | `datosPaciente.xhtml` |
| 6 | ¿Alta paciente desde buscador? | **diferido** — botón *Nuevo paciente* oculto v1. | `popUpBuscadorPaciente` |
| 7 | ¿Elegibilidad WS? | **diferido** `turnos-agenda-elegibilidad-cobros`. v1: campos afiliado/doc editables; **icono validador oculto**. | D-TUR-25 |
| 8 | ¿Drag ficha → grilla? | **diferido** `turnos-agenda-drag-drop`. | draggable west |
| 9 | ¿Otros centros — gear? | **Asignar** + **Información** in-scope. **Consultar agenda** **diferido** `turnos-agenda-consultas`. | tieredMenu otros centros |
| 10 | ¿Estrategia datos? | JDBC lectura `ts.paciente` + `ts.persona` + convenio/plan; búsqueda paginada; sin Oracle runtime. | V30/V31 · V47 seed |
| 11 | ¿Viaje Playwright? | **e2e-migrado** ampliación: buscar paciente → ficha west → otorgar. | `turnos-agenda.spec.ts` |
| 12 | ¿Prerrequisito T5 smoke ops? | Recomendado G6; no bloquea G1–G5 dev. | TSK-ops-g6-1 T5 |

**Firma producto:** Camino 1 aceptado **2026-09-07** → G0 inventarios done; siguiente **G1** DDL (si aplica) + **G2** API.

### Flujo north (paridad simplificada)

```
/turnos/agenda
  tipear apellido / Buscar → dialog paciente → elegir fila
    → completa north (conv/plan/afiliado/doc) + west ficha + refresca otros centros
  tipear convenio / Buscar → dialog convenio → plan combo
  Limpiar paciente → vacía north paciente + west + ids en session
  Consultar grilla (T5) — usa ids north reales, no demo hardcode
```

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Buscar paciente (apellido) | `buscarPacienteAgenda` · `buscadorPaciente.xhtml` | **In scope** |
| Buscar paciente por documento | `buscarPacienteAgendaPorDocumento` | **In scope** (north + dialog) |
| Buscar convenio | `buscarConvenioAgenda` · `buscadorConvenio.xhtml` | **In scope** |
| Combo plan convenio | `selectItemPlanConvenio` · `actChangePlanConvenio` | **In scope** |
| Limpiar datos paciente | `limpiarDatosPaciente` | **In scope** |
| Info búsqueda paciente | `popupInfoBusqueda` | **In scope** (imagen/help) |
| Info convenio / obs plan | `popupObservacionesPlanConvenio` | **In scope** (lectura) |
| Ficha west read-only | `formMenuPaciente` | **In scope** |
| Desde / Hasta calendario | `horaDesde` / `horaHasta` · `onChangeHoraTurno` | **In scope** |
| Leyenda calendario 6 colores | `formMenuIzquierdo` south grid | **In scope** |
| Otros centros (lista) | `selectTurnosOtrosCentros` | **In scope** |
| Asignar desde otros centros | gear → `actionBtnReservarTurno` | **In scope** |
| Consultar agenda otros centros | `actBtnConsultaOtrosCentros` | **diferido** `turnos-agenda-consultas` |
| Accordion tab Paciente (170px) | `asignacionTurnos.xhtml` | **diferido** `turnos-asignacion-shell` |
| ABM datos paciente (tabs) | `datosPaciente.xhtml` | **diferido** (sub-slugs) |
| Nuevo paciente | `actBtnNuevoPaciente` | **diferido** ABM pacientes |
| Validador elegibilidad WS | `validarElegibilidad` | **diferido** D-TUR-25 |
| Drag-drop reserva | droppable columna paciente | **diferido** drag-drop hijo |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | `GET …/pacientes/buscar` (q, page) → id + labels (apellido, nombre, doc, conv/plan última atención) |
| RF-2 | `GET …/pacientes/{id}/ficha` → campos west (nombre, doc, FN, edad, sexo, conv/plan, afiliado, obs, tel, mails) |
| RF-3 | `GET …/convenios/buscar` (q, page) → id + label |
| RF-4 | `GET …/convenios/{id}/planes` → lista plan convenio |
| RF-5 | `GET …/otros-centros` (idPaciente, idPrestacion, centros, fechaDesde/Hasta, …) → filas tabla legacy |
| RF-6 | North Web: dialogs paciente/convenio + wiring a filtros grilla T5 (reemplazar ids demo) |
| RF-7 | West Web: Desde/Hasta filtran grilla/calendario como legacy (misma API query params T5) |
| RF-8 | West Web: ficha sincronizada al seleccionar paciente |
| RF-9 | Gate UI: inventarios copy + interacción + validaciones + geometría xhtml **antes** template |
| RF-10 | e2e: buscar paciente + otorgar (extiende `turnos-agenda.spec.ts`) |
| NFR-1 | Capas starter; endpoints bajo `/api/v1/turnos/agenda/...` |
| NFR-2 | IT búsqueda + ficha + otros centros con seed V47/V31 |
| NFR-3 | Canon DDL `ts`; sin `*_agi` |
| NFR-4 | Paridad UI v1.12 (interacción/disparador); toast/modal legacy |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-31 | Slug = `turnos-agenda-ficha-paciente` (hijo T5, no reabrir T5 gate) |
| D-TUR-32 | Misma ruta `/turnos/agenda`; T5 e2e sigue válido; ampliar escenarios |
| D-TUR-33 | Shell 170px accordion → `turnos-asignacion-shell` |
| D-TUR-34 | Elegibilidad WS → `turnos-agenda-elegibilidad-cobros` (después de este corte) |
| D-TUR-24 | (heredada T5) ficha west → **este slug** |
| D-TUR-35 | **Camino 1 FIRME:** T5.1 cierra west+north en `/turnos/agenda`; shell 170px después |

## No objetivos

| Ítem | Destino |
|------|---------|
| Reservar/otorgar/liberar núcleo | T5 (hecho) |
| Elegibilidad / cobros / CTA | `turnos-agenda-elegibilidad-cobros` |
| Menú turnero 170px completo | `turnos-asignacion-shell` |
| Tabs ABM paciente turnero | hijos / configuración pacientes |
| Repetidos / múltiples / pre-agenda | hijos T5 |
| Reasignar | T6 |

## Evidencia legacy (paths)

```
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/agenda.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/asignacionTurnos.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/buscadores/buscadorPaciente.xhtml
Hospital-Legacy/HOSPITAL_2/WebRoot/pages/buscadores/buscadorConvenio.xhtml
Hospital-Legacy/HOSPITAL_2/src/.../BBAgenda.java
Hospital-Legacy/HOSPITAL_2/src/.../BBAsignacionTurnos.java
Hospital-Legacy/HOSPITAL_2/src/.../BBBuscadorPaciente.java
Packages/Turnos/TURNOS.PACKAGE_BODY.sql (selectTurnosOtrosCentros)
Hospital-Api/.../V31__ts_agi_maestros.sql
Hospital-Api/.../V47__* (seed T5 demo)
```
