---
title: Spec — M1b ABM servicio + servicio_centro
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1b-servicio.spec
---

# Spec — M1b servicio + vínculo

Padre: [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/) · **M1b**

## Problema

Catálogo `ts.servicio` y vínculo `ts.servicio_centro` no tenían ABM en la plataforma nueva.

## Resultado

1. ABM `servicio` (10002): listado HAB (búsqueda + tabla + Agregar) + dialog nombre `(*)`.
2. ABM vínculo `servicio_centro` (10204): listado HAB (q 320px + filtro centro 258px + amb 130px) + dialog hoja datos (sin tabs amb/int/lab, sin blobs). Recarga con overlay global, no `Cargando…` en el form.
3. Evidencia: `id_servicio` y par `(id_centro_ate, id_servicio)` nacidos del CU.

## Universo firmado

Índice 2026-09-16:

- `--semilla servicio`: 3 xhtml / 2 beans · techo ok.
- `--semilla pages/configuracion/servicioCentro/servicioCentro`: 3 xhtml / 2 beans · techo ok.
- Ambiguo `servicioCentro` (convenio/lab) → **fuera** (CU-A / Lab).

| Entra | Path |
|-------|------|
| `servicio.xhtml` + `buscadorServicio.xhtml` | catálogo |
| `servicioCentro/servicioCentro.xhtml` + `buscadorServicioCentro.xhtml` | vínculo |
| `datosServicioCentro.xhtml` (1 hop menú west datos) | alta vínculo |
| Beans `BBServicio` · `BBBuscadorServicio` · `BBServicioCentro` · `BBBuscadorServicioCentro` | |

| Fuera | Destino |
|-------|---------|
| `convenio/servicioCentro.xhtml` · `laboratorio/servicioCentro.xhtml` | CU-A / Lab |
| West amb/int/lab y resto | [`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/) |
| `especialidad.xhtml` | [`maestros-m1c-especialidad`](../maestros-m1c-especialidad/) |
| Firmas PL/SQL A/B | ninguna en semillas |
| `InsertarDeterminacionServicioJob` | **N/A** Lab |
| `CheckHabTurnosJob` | `diferido(relevamiento-procesos-programados)` |
| `param_atencion_serv` (insert extra en Aceptar HIS) | **fuera** este wave |

Techo combinado ~6–8 xhtml: **ok** sin especialidad ni tabs.

## Clarify

| # | Respuesta |
|---|-----------|
| 1 | Pipeline: catálogo servicio **no** espera centro. Vínculo sí (M1a id vigente). Perfil tile AG. |
| 2 | Alta servicio (nombre `(*)`) → Aceptar en dialog → toast. Listado HAB (firma 2026-09-17b). |
| 3 | DELETE físico servicio (`desea_eliminar_el_servicio`). Vínculo: `desea_eliminar_servicio_centro`. |
| 4 | Vínculo: centro M1a `id_centro_ate=1002` desbloquea RF-S2. Actor sin tile. |
| 5 | Sin TV/PDF. Job hab **diferido**. No insertar `param_atencion_serv`. |
| 6 | Especialidad / tabs / convenio-serv → hijos. No WAIVE. |
| 7 | Inventarios G0 copy/validaciones. Chrome listado HAB (no west HIS). Campo 245px en dialog catálogo. |
| 8 | Playwright `diferido(fixture)`. |

## Capacidades

| Capacidad | Legacy | Escritura | Este slice |
|-----------|--------|-----------|------------|
| ABM servicio | `BBServicio` · 10002 | INSERT/UPDATE/DELETE `servicio` | **In scope** |
| Buscar servicio | `buscadorServicio.xhtml` | Lee | **In scope** |
| ABM vínculo datos | 10204 · `datosServicioCentro` | INSERT/UPDATE/DELETE `servicio_centro` | **In scope** |
| Buscar vínculo | `buscadorServicioCentro.xhtml` | Lee | **In scope** |
| Tabs amb/int/lab | west mismo shell | Sí | **diferido(maestros-m1b-servicio-tabs)** |
| Especialidad | 10003 | Sí | **diferido M1c** |
| `atiende_turnos` job | `CheckHabTurnosJob` | UPDATE | **diferido(jobs)** — persistir default `N` al insert |

## RF escritura

| RF | Crear | Visible | Fin |
|----|-------|---------|-----|
| RF-S1 servicio | INSERT | GET/buscar | DELETE |
| RF-S2 vínculo | INSERT par `(id_centro_ate, id_servicio)` | GET/buscar | DELETE |

## NFR

Escritura p95 ≤ 1,5 s. Volumen `diferido(perf-volumen)`. Concurrencia: PK `id_servicio` (NextId) y par único `(id_servicio, id_centro_ate)` — dos actores no duplican el par (23505). Sin `FOR UPDATE` en el bean del catálogo; el job hab no se reabre.

## Acceso

Menú 10002 y 10204 bajo AG. Sin `Raise_application_error` de rol en el bean de servicio (paridad: menú). Prueba actor sin tile.

## PL/SQL

Ninguna firma a portar. NextId reuso en catálogo. Vínculo: PK compuesta, sin NextId.

## Criterios

1. G0 inventarios copy/validaciones. Chrome listado HAB (firma producto).
2. IT + **id** servicio y par vínculo.
3. Sin Flyway.
4. Playwright `diferido(fixture)`; NFR `diferido(perf-volumen)`; acceso actor sin tile ejecutado.
