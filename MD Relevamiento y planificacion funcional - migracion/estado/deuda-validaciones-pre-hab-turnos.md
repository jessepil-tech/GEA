---
title: Deuda — Inventario validaciones en pantallas previas a habilitación turnos
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.deuda-validaciones-pre-hab-turnos
---

# Deuda — Validaciones BB/MessageBundle (pantallas pre-T2)

## Decisión

El gate **Inventario validaciones** ([`regla-paridad-ui-legacy.md`](../canon/regla-paridad-ui-legacy.md)
v1.4+) se formalizó en **2026-08-28** (lección T3 g4-3). Las pantallas migradas
**antes de habilitación de turnos (T2)** **no** tienen ese inventario ni la paridad
UI+API de mensajes BB/`MessageBundle`.

**No es WAIVE:** legacy sí tiene validaciones. Estado = **diferido** hasta abrir SDD
hijo o reabrir el corte al tocar la pantalla.

**Corte histórico:** todo lo gate-done / parcial **antes** de
[`turnos-config-hab-horarios/`](../cortes/turnos/turnos-config-hab-horarios/) (T2, 2026-08-27).

**Nota T2/buscadores:** T2 y `turnos-hab-buscadores*` cerraron **antes** de v1.4;
tampoco traen fila “Inventario validaciones” en verify. Al retocar hab → mismo
tratamiento (inventario o `diferido(slug)`), no asumir done.

Plantilla: [`turnos-horarios-grupos/inventario-validaciones.md`](../cortes/turnos/turnos-horarios-grupos/inventario-validaciones.md).

## Inventario de deuda (pantallas / cortes)

| Ruta Web (aprox.) | SDD | Estado validaciones v1.4 | Acción |
|-------------------|-----|--------------------------|--------|
| `/catalogo/convenios` | [`cu-clinico-a-catalogo-abm/`](../cortes/recepcion/cu-clinico-a-catalogo-abm/) | **faltante** | Al retocar ABM: inventario + UI/API |
| `/agi/recepcion` | [`piloto-agi-g1/`](../cortes/recepcion/piloto-agi-g1/) (+b/c/d) | **faltante** | Idem (piloto medidor; no ensanchar seed) |
| `/agi/espera` | [`cu-clinico-b-post-recepcion/`](../cortes/recepcion/cu-clinico-b-post-recepcion/) · B1 | **faltante** | Idem |
| `/agi/demanda-espontanea` | [`cu-clinico-c-demanda-espontanea/`](../cortes/recepcion/cu-clinico-c-demanda-espontanea/) | **faltante** | Idem |
| `/recepcion/cola` | [`paridad-recepcion-cola/`](../cortes/recepcion/paridad-recepcion-cola/) | **faltante** | Idem (popups / acciones) |
| `/recepcion/espera-amb` | mismo | **faltante** | Idem |
| `/anunciadores` (+ detalle) | [`piloto-agi-anunciador/`](../cortes/anunciador/piloto-agi-anunciador/) · ciclo vida | **faltante** / N/A ops | Solo si hay ABM/forms con BB |
| `/turnos/inicio` (T1) | [`turnos-maestros-personal/`](../cortes/turnos/turnos-maestros-personal/) | **faltante** (gate parcial) | Incluir en cierre T1 |
| `/auth/*` · `/profile` | [`identidad-oleada-a/`](../cortes/plataforma/identidad-oleada-a/) | revisar al tocar | Solo forms con paridad xhtml |
| `/configuracion/hab-turnos-*` | [`turnos-config-hab-horarios/`](../cortes/turnos/turnos-config-hab-horarios/) | **faltante** (pre-v1.4) | Hijo o fila al próximo cambio |
| Buscadores hab | [`turnos-hab-buscadores/`](../cortes/turnos/turnos-hab-buscadores/) · filtros | N/A dialog / parcial | Criterios required si legacy exige |

Mapa rutas: [`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md).

## Cómo cerrar un ítem

1. Abrir/actualizar SDD del corte (o hijo `…-validaciones`).
2. Tabla inventario validaciones (BB + MessageBundle → UI + API).
3. Implementar create **y** update; mensajes literales.
4. Fila en verify **Paridad UI** → Inventario validaciones = done / diferido restante.
5. Actualizar esta tabla (done + fecha) y [`backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md).

## Qué no hacer

- Reabrir en bloque todo el piloto “para validaciones” sin corte.
- Marcar WAIVE por velocidad.
- Dar por cubierto un ABM pre-T2 solo porque el happy path guarda.
