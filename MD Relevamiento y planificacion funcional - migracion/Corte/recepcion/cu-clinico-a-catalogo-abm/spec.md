---
title: SDD — CU clínico A · Catálogo ABM convenios
description: Primer vertical CRUD CQRS + UI (convenio_agi).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-a-catalogo-abm
---

# Spec — CU-A Catálogo ABM (convenios)

Padre: [`cu-clinico/`](../cu-clinico/).

## Problema

El piloto AGI usa convenios **seed** (solo lectura vía recepción). Hay que medir el
costo de un **ABM mínimo** en el stack nuevo (patrón que se repetirá en Fase 4).

## Resultado

1. `GET/POST /api/v1/catalogo/convenios` y `GET/PUT .../{id}` con JWT Identity.
2. Persistencia en `convenio_agi` (Postgres).
3. UI Hospital-Web `/catalogo/convenios` (lista + alta/edición).
4. IT + smoke.

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Listar convenios |
| RF-2 | Obtener por id |
| RF-3 | Crear (codigo único, nombre, flags G1-b/c) |
| RF-4 | Actualizar |
| RF-5 | UI autenticada |
| NFR-1 | CatalogPort aparte de AgiPort |
| NFR-2 | Sin microservicio Core |

## No objetivos (este slice)

- ABM de todas las tablas de configuración
- Soft-delete / histórico en `convenio_agi`
- Permisos finos por rol (JWT autenticado basta)

## Paridad legacy (regla WAIVE)

| Superficie | ¿ABM convenios? | Decisión |
|------------|-----------------|----------|
| **AGI** (tótem / recepción) | **No** — solo selección de convenio seed | RFs CU-A = capacidad **nueva** del piloto (medir patrón CRUD); no hay función legacy AGI que WAIVEar |
| **HOSPITAL_2** `configuracion/convenio/convenio.xhtml` (`BBConvenio`: buscar/agregar/**eliminar**, planes, alias, …) | **Sí** — módulo config completo | **No** es el objeto de CU-A (entidad Oracle distinta a `convenio_agi`). Deuda de paridad → **Fase 4 / catálogo master** (slug SDD cuando se abra), **no** WAIVE de “eliminar” en este gate |

Evidencia AGI sin ABM: `AGI/.../recepcion/parts/convenio(s).xhtml` (selección).  
Evidencia HOSPITAL_2: `HOSPITAL_2/.../configuracion/convenio/convenio.xhtml` `actBtnEliminarConvenio`.

## Criterios de aceptación

1. Smoke CRUD PASS.
2. Código duplicado → 409.
3. UI lista y guarda un convenio.
4. Recepción G1 sigue funcionando con seeds.
