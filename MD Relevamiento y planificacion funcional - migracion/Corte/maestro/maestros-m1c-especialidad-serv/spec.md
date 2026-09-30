---
title: Spec — vínculo especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.spec
---

# Spec — M1c hijo especialidad-servicio-centro

Padre: [`maestros-m1c-especialidad/`](../maestros-m1c-especialidad/) · menú **10204** west tab `msg.especialidad`.

## Problema

`ts.especialidad_serv` no tiene ABM en la plataforma nueva. M1c solo porta el catálogo.

## Resultado

ABM HAB: filtros (q especialidad + centro 258px + servicio) + tabla + Agregar + dialog
600px (`especialidad (*)` deshabilitado al editar, `numero_orden (*)`, flags, internación,
modalidad, etiquetas, mensaje espera). Evidencia = triple PK nacido del CU.

## Universo firmado

Índice 2026-09-18 `indice-legacy.sh --semilla especialidadServ`: 2 xhtml / 2 beans · techo **ok**.
`--jobs especialidad_serv`: vacío → **N/A**. Firmas PL/SQL: ninguna.

| Entra | Path |
|-------|------|
| `especialidadServ.xhtml` | hoja west |
| `BBEspecialidadServ` | insert/update/delete |

| Fuera | Destino |
|-------|---------|
| `servicioCentro.xhtml` + `BBServicioCentro` | [`maestros-m1b-servicio-tabs`](../maestros-m1b-servicio-tabs/) (shell west) |
| `buscadorEspecialidadServ.xhtml` | absorbido por searchBar HAB |
| Quirúrgica / west centro `datosEspecialidad` | otros slugs |

Chrome listado HAB (firma 2026-09-17b), no `pe:layoutPane`. En HAB el par centro+servicio
va al dialog (en HIS vive en el padre west).

## Clarify

| # | Respuesta |
|---|-----------|
| 1 | Pipeline: padres centro/servicio/especialidad ya ABMeados. Perfil tile AG 10204. |
| 2 | Alta triple + orden `(*)` + flags → Aceptar → toast `ROW_INSERT_INFO`. |
| 3 | DELETE físico; confirm `desea_eliminar_especialidad`. |
| 4 | Actor sin tile 10204. Sin `Raise_application_error` de rol en el bean. |
| 5 | Sin TV/PDF/jobs. Sin NextId (PK compuesta). |
| 6 | Tabs amb/int/lab y quirúrgica fuera. No WAIVE. |
| 7 | G0. Dialog 600px, label 100px, `InputWid100`. `closable=false`. |
| 8 | Playwright `diferido(fixture)`. |

## Capacidades

| Capacidad | Legacy | Escritura | Este slice |
|-----------|--------|-----------|------------|
| ABM especialidad por par servicio-centro | `BBEspecialidadServ` | INSERT/UPDATE/DELETE | **In scope** |
| Buscar por nombre especialidad | `selectLikeBuscador` | Lee | **In scope** (q) |

## RF escritura

| RF | Crear | Visible | Fin |
|----|-------|---------|-----|
| RF-ES1 | INSERT triple | GET/list | DELETE |

## NFR

Escritura p95 ≤ 1,5 s. Volumen `diferido(perf-volumen)`. Concurrencia: PK triple
(23505). Sin `FOR UPDATE` en el bean.

## Acceso

Menú 10204 bajo AG. Prueba actor sin tile.

## PL/SQL

Ninguna firma. Rareza: `replaceAccentedChars` en `modalidad` y `mensaje_espera_serv_centro`.

## Criterios

1. G0 + chrome HAB.
2. Triple PK en dump (`id=` de cada parte en el ledger).
3. Sin Flyway.
4. Playwright `diferido(fixture)`; NFR `diferido(perf-volumen)`; acceso sin tile.
