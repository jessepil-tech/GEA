---
title: Spec — M1c ABM especialidad
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad.spec
---

# Spec — M1c especialidad

Padre: [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/) · **M1c**

## Problema

Catálogo `ts.especialidad` (menú 10003) no tenía ABM en la plataforma nueva.

## Resultado

ABM HAB: búsqueda + tabla + Agregar + dialog (`especialidad (*)` 245px, interconsulta, `adulto_pediatrico (*)`). Evidencia = `id_especialidad` nacido del CU.

## Universo firmado

Índice 2026-09-17 `indice-legacy.sh --semilla especialidad`:

- 3 xhtml / 2 beans · techo **ok**.
- Firmas PL/SQL: ninguna.
- `--jobs especialidad`: sin coincidencias → **N/A**.

| Entra | Path |
|-------|------|
| `especialidad.xhtml` + `buscadorEspecialidad.xhtml` | catálogo |
| Beans `BBEspecialidad` · `BBBuscadorEspecialidad` | |

| Fuera | Destino |
|-------|---------|
| `contractDefault.xhtml` | tronco plantilla |
| `especialidadServ.xhtml` | vínculo servicio (otro corte) |
| `especialidadQuirurgica` | otro dominio |
| `centroAtencion/datosEspecialidad.xhtml` | [`maestros-m1a-centro-west`](../maestros-m1a-centro-west/) |
| `consultaPersonalEspecialidad` | RRHH |

## Clarify

| # | Respuesta |
|---|-----------|
| 1 | Pipeline: sin padres de M1a/M1b. Perfil tile AG 10003. |
| 2 | Alta nombre `(*)` + adulto/pediátrico `(*)` + interconsulta → Aceptar dialog → toast. Listado HAB. |
| 3 | DELETE físico (`desea_eliminar_especialidad`). |
| 4 | Actor sin tile. |
| 5 | Sin TV/PDF/jobs. NextId `ESPECIALIDAD` vía `sec_id_tabla`. |
| 6 | Vínculo especialidad-servicio / quirúrgica → fuera. No WAIVE. |
| 7 | G0 copy/validaciones. Chrome HAB (no west HIS). Campo 245px en dialog. |
| 8 | Playwright `diferido(fixture)`. |

## Capacidades

| Capacidad | Legacy | Escritura | Este slice |
|-----------|--------|-----------|------------|
| ABM especialidad | `BBEspecialidad` · 10003 | INSERT/UPDATE/DELETE | **In scope** |
| Buscar | `buscadorEspecialidad.xhtml` | Lee | **In scope** (absorbido por listado) |

## RF escritura

| RF | Crear | Visible | Fin |
|----|-------|---------|-----|
| RF-E1 | INSERT | GET/buscar | DELETE |

## NFR

Escritura p95 ≤ 1,5 s. Volumen `diferido(perf-volumen)` (dump vacío). Concurrencia: NextId `id_especialidad`. Sin `FOR UPDATE` en el bean.

## Acceso

Menú 10003 bajo AG. Sin `Raise_application_error` de rol en el bean. Prueba actor sin tile.

## PL/SQL

Ninguna firma a portar. NextId reuso. Rareza HIS: `replaceAccentedChars` (mayúsculas, sin acentos) en el nombre.

## Criterios

1. G0 inventarios. Chrome HAB.
2. **id** nacido del CU.
3. Sin Flyway.
4. Playwright `diferido(fixture)`; NFR `diferido(perf-volumen)`; acceso actor sin tile ejecutado.
