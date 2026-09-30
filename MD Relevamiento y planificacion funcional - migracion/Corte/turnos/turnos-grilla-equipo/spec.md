---
title: Spec — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo.spec
---

# Spec — Grilla en modo equipo

Semillas: `generacionGrillaTurnos`, `eliminarGrillaTurnos`, `consultaAgendasGeneradas`.  
Un hop por hoja: `buscadorEquipoServCentro.xhtml` y `buscadorPersonalServicio.xhtml`. Tronco `BBSessionData` solo por sesión.  
El segundo hop (Agenda, historial, suspender, cola) no entra.

## Clarify — **FIRME** (ealbo, 2026-09-24)

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|----------|---------------------|-----------|
| 1 | ¿Pipeline? | T4 generó serv/pers. D-TUR-13 cerró el horario de equipo. Este corte enciende el radio Equipo de las mismas tres hojas. | spec T4 D-TUR-17 · índice 2026-09-23 |
| 2 | ¿Happy path? | Radio Equipo → buscar `EQDEMO1001` → grupo del equipo → fechas → Generar. La consulta de agendas filtra por ese equipo. Eliminar usa el mismo radio. | `BBGeneracionGrillaTurnos` tipo `EQUIPO` |
| 3 | ¿Ciclo de vida? | El turno nace `LIBRE` con el equipo. Eliminar lo saca; si tenía paciente, pasa a la cola de reasignar (igual que T4). | T4 RF-4 / RF-7 |
| 4 | ¿Errores / acceso? | `Debe seleccionar un equipo.` (`REQUIRED_EQUIPO`) al generar y al consultar. Al eliminar, `EQUIPO_REQUIRED_ERROR`. Menú TURNOS, el mismo de T4 (`ATENCION_TURNOS`). Rol funcional en estos beans: **ninguno**. Actor sin la entrada de menú no entra. | MessageBundle · beans |
| 5 | ¿Side-effects? | Toast de fin y tabla de observaciones, igual que serv/pers. Sin mail nuevo. | T4 |
| 6 | ¿Fuera? | Combo Equipo de Agenda, historial, suspender, cola, consulta de agenda y sobreturno. Horario especial dentro del cálculo. Jobs de laboratorio / ANMAT. Impresión de la consulta (hijo ya cerrado). | slugs abajo |
| 7 | ¿Paridad UI? | El radio y el input ya están en la pantalla migrada, apagados. Este corte los enciende. No se redibuja la hoja. Inventarios de este slug antes de tocar el component. | `grilla-turnos-*.component.ts` |
| 8 | ¿Viaje Playwright? | **e2e-migrado**: generar, eliminar y consulta en `e2e/grilla-turnos-equipo.spec.ts`. Corrido 2026-09-24, 3 passed. | verify |

### Decisiones de este tramo

| Id | Decisión |
|----|----------|
| D-TUR-17 | Se parte. Este slug cobra generar, eliminar y consultar en modo equipo. |
| Agenda | El filtro Equipo de otorgar y de las hojas que lo dejaron apagado va a [`turnos-agenda-equipo`](../turnos-agenda-equipo/). |
| D-TUR-15 | Horario especial dentro del cálculo sigue fuera. |
| D-TUR-19 | Mail al eliminar sigue en T7. |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Generar con radio Equipo | `generacionGrillaTurnos.xhtml` · `TipoFiltro.EQUIPO` | **en alcance** |
| Eliminar con radio Equipo | `eliminarGrillaTurnos.xhtml` · `EQUIPO_REQUIRED_ERROR` | **en alcance** |
| Consultar agendas por equipo | `consultaAgendasGeneradas.xhtml` · combo `grp_prest_equipo` | **en alcance** |
| Buscador equipo | `buscadorEquipoServCentro.xhtml` | **en alcance** (lee; no crea el vínculo) |
| Generar/eliminar serv y pers | T4 | **N/A** — ya cerrado; no se reabre |
| Filtro Equipo en Agenda y hermanas | radios disabled D-TUR-17 | **diferido** [`turnos-agenda-equipo`](../turnos-agenda-equipo/) |
| Horario especial en el cálculo | `dia_hora_esp_tur_grp_*` | **diferido** D-TUR-15 |
| Impresión de la consulta | hijo T4 | **N/A** — no se reabre |
| Jobs `--jobs grilla` / `--jobs agendas` | sin coincidencias | **N/A** |
| Jobs `--jobs generacion` | asientos, ocupación, honorarios, pedido | **N/A** (no arman turnos) |
| Jobs `--jobs equipo` | 5 de laboratorio / ANMAT | **N/A** (no arman la grilla) |

## Firmas PL/SQL

El índice no ve `f_`/`p_` en los beans: delegan en business.  
`TS.TURNOS.f_gen_grilla_turnos`, `TS.TURNOS.f_elim_grilla_turnos` y `TS.TURNOS.f_consulta_agendas_generadas`: **rediseñar**, decisión ya tomada en T4 para serv/pers. Este corte extiende esa misma implementación al modo equipo. No se porta el package ni se abre puente nuevo.

## Presupuesto no funcional

| Eje | Presupuesto |
|-----|-------------|
| Tiempo | p95 de Generar en modo equipo, a medir en el paso 6 |
| Volumen | **medido en vacío** (un equipo, un horario) + `diferido(perf-volumen)` |
| Concurrencia | Recurso = rango de fechas del mismo equipo. Dos Generar solapados: uno materializa y el otro observa el choque. En el repo no aparece `FOR UPDATE` sobre este acto; el control es el de T4 |

## Instalación de referencia

Call Center Demo. Repo `cliente="TS"`: las ramas `esClienteX()` quedan apagadas. Estas hojas no tienen ninguna.

## Acceso

Perfil: quien ve TURNOS → Generar / Eliminar / Consulta agendas (el mismo de T4).  
Rol funcional PL/SQL: ninguno en estos beans.  
Prueba negativa: actor sin esa entrada de menú.
