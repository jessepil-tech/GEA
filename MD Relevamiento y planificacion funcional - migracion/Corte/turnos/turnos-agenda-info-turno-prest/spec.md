---
title: Spec — prep / requisitos realización infoTurno
version: 1.0.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest
---

# Spec — Preparación Previa y Requisitos Realización

Padre: [`turnos-agenda-info-turno/`](../turnos-agenda-info-turno/) (chrome **gate-done**; filas vacías).  
Ruta: **`/turnos/agenda`**.  
**FIRME** Camino 1 — 2026-09-10 (Francisco: «ok firme»). **gate-done** G6 **PASS** 2026-09-10 («se ve bien»). D-TUR-49 · D-TUR-50.

Relevamiento: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/). Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

## Problema

HIS, al abrir `infoTurno`, carga preparación HTML y la tabla de requisitos de **esa** prestación. T5.1e dejó el chrome; los paneles siguen vacíos (emptyMessage). No hay tablas ni GET en el Api.

## Resultado (objetivo Camino 1)

Misma ruta, mismo dialog T5.1e. Al abrir **Asignar** o **Información** (un-turno): mostrar preparación (primera fila por edad) y filas de requisitos de la prestación. Lectura; **no** se graba en `ts.turno`.

## Clarify — **FIRME** Camino 1 (2026-09-10)

| # | Pregunta | Respuesta | Evidencia |
|---|---------|-----------|-----------|
| 1 | ¿Pipeline? | T5.1e chrome **gate-done**. Ficha (fecha nac) T5.1. Prestación del slot (grilla). Tablas `preparacion_prest` / `req_realiza_prest` / `req_realizacion` **no** están en Flyway → **G1**. Seed demo `CONS` / `80001` (V42). ABM maestros **fuera** (seed ≠ paridad config). | padres · V42 · hbm |
| 2 | ¿Misma ruta? | **Sí** `/turnos/agenda`. Mismo dialog T5.1e. LIBRE Asignar, Información, post-sobreturno. | `infoTurno.xhtml` L150–232 |
| 3 | ¿Happy path? | Al abrir: GET prep+req con `cod_prestacion` + `id_prestacion` + fecha nac ficha. Prep: primera fila cuyo rango de edad cubre al paciente; HTML `escape=false`. Req: filas Documentación (`req_realizacion`) + Observaciones. Sin filas → emptyMessage ya en chrome. | BB `BBAsignacionTurnos` L697–710 · `ImpPrestacion.selectPreparacionPrestEdad` |
| 4 | ¿Ciclo de vida? | Solo lectura. Cancelar / otorgar / liberar **no** escriben prep/req. Si el slot no tiene `id_prestacion`, no llama (HIS `if idPrestacion != null`). Fecha nac nula → edad 0 (HIS `calcularEdad` catch). | BB L697 · `DateCommonFunctions` L198–205 |
| 5 | ¿Errores? | GET fallido → toast Api sin `/500`. Sin MessageBundle de bloqueo en este acto (HIS no toasta si no hay prep). | T5 feedback |
| 6 | ¿Fuera? | Print prep (solo `UserSession.recepcion`) **N/A** agenda call center / T7. Concat `<br/>` repetidos/múltiples → `turnos-agenda-repetidos`. Merge `req_realiza_prest_equipo` → D-TUR-17 / equipo. ABM maestros. Doc-req convenio = T5.1c (ya). | xhtml L154–159 · BB L590–595 · L712–731 |
| 7 | ¿Gate UI? | Chrome **ya** (prep 450×260; req 360×202; emptyMessage). Este corte **rellena** el `div` HTML y las filas de la tabla. **No** nuevo dialog ni rehacer 1200×520. HTML HIS `escape=false`. Print **no** en call center. | T5.1e inventarios · regla v1.13 |
| 8 | ¿Playwright? | **e2e-migrado:** abrir infoTurno con fixture/seed CONS → prep visible + fila req. Sin datos → emptyMessage (ya chrome). Legacy **no**. | `turnos-agenda.spec.ts` |
| 9 | ¿API? | **GET** `/api/v1/turnos/agenda/prep-req` en el mismo `TurnosAgendaResource`. Query: `codPrestacion`, `idPrestacion`, `fechaNacimiento`. Un round-trip prep + lista req. Resource delgado; JDBC `ts`. | scaffold |

**Camino 1** = un-turno: GET + llenar los dos paneles + G1 DDL/seed.  
No hay Camino “solo un panel”: HIS muestra los dos juntos.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Prep HTML al abrir (1ª fila por edad) | BB L701–703 · `outputText escape=false` | **In scope** | No (GET) |
| Tabla req realización de la prestación | BB L706–710 · xhtml L222–232 | **In scope** | No (GET) |
| EmptyMessage si no hay filas | `no_se_encontraron_registros` | **In scope** (chrome ya) | No |
| Filtro edad `edadDesde`/`edadHasta` null-or-range | `ImpPrestacion` L168–172 | **In scope** | No |
| Seed demo CONS/80001 | V42 prestación | **In scope** G1 | INSERT seed |
| Merge req por equipo | BB L712–731 | **diferido** D-TUR-17 | — |
| Concat prep repetidos/múltiples | BB L590–595 `<br/>` | **diferido** `turnos-agenda-repetidos` | — |
| Imprimir preparación | xhtml L154–158 recepción | **N/A** agenda / T7 | — |
| ABM `preparacion_prest` / req | `insertPreparacionPrest` config | **fuera** (seed ≠ ABM) | — |
| Doc-req convenio | T5.1c | **reusa** | — |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | Al abrir Asignar/Información un-turno, GET prep+req de la prestación del slot. |
| RF-2 | Preparación: HTML de la primera fila cuyo rango cubre la edad (hoy − fecha nac, fórmula HIS). Vacío = panel en blanco. |
| RF-3 | Requisitos: filas Documentación + Observaciones; vacío = emptyMessage. |
| RF-4 | No escribe `ts.turno`. |
| RF-5 | Gate UI: rellenar chrome existente **antes** de pedir smoke. |
| RF-6 | e2e con seed/mocks CONS. |
| NFR-1 | Resource delgado; schema `ts`; `PREPARACION` = `TEXT` (catálogo BLOB→TEXT). |
| NFR-2 | RF de escritura: **N/A** (solo seed G1, no CU de negocio). |

## Decisiones **FIRME**

| Id | Decisión |
|----|----------|
| D-TUR-49 | Lectura al **abrir** infoTurno (Asignar e Información). Un GET en `TurnosAgendaResource`. No GET infoTurno de turno. |
| D-TUR-50 | G1: `CREATE` `ts.req_realizacion`, `ts.preparacion_prest`, `ts.req_realiza_prest` + seed CONS/`80001`. `preparacion` `TEXT` UTF-8 (catálogo BLOB→TEXT). Copia BLOB Oracle WIN1252 → no este corte (playbook BIRT). |
