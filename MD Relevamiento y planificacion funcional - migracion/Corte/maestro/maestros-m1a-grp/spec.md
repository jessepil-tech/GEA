---
title: Spec — M1a ABM grupo centro de atención
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.spec
---

# Spec — M1a grp

Padre: [`maestros-m1a-centro/`](../maestros-m1a-centro/) · menú **10217**.

## Problema

M1a solo GET+seed del combo grupo. Gatillo del pagaré: alta real de grupo desde UI (no seed).

## Resultado

ABM HAB: búsqueda + tabla + Agregar + dialog (`grp_centro_atencion (*)` + checkbox `permite_elegir_centro_turnos`, default HIS **S**). Evidencia = `id_grp_centro_ate` nacido del CU.

## Universo firmado

Índice 2026-09-18 `indice-legacy.sh --semilla grpCentroAtencion`:

- 3 xhtml / 2 beans · techo **ok**.
- Firmas PL/SQL: ninguna.
- `--jobs grp_centro`: sin coincidencias → **N/A**.

El índice no lista `datosGrpCentroAtencion.xhtml` (hijo de template) ni `BBDatosGrpCentroAtencion`. Entran por hop de formulario.

| Entra | Path |
|-------|------|
| `grpCentroAtencion.xhtml` + `datosGrpCentroAtencion.xhtml` + `buscadorGrpCentroAtencion.xhtml` | catálogo 10217 |
| Beans `BBGrpCentroAtencion` · `BBDatosGrpCentroAtencion` · `BBBuscadorGrpCentroAtencion` (absorbido) | |

| Fuera | Destino |
|-------|---------|
| `contractDefault.xhtml` | tronco plantilla |
| ABM `centro_atencion` | padre gate-done |
| west logo/sector/caja | [`maestros-m1a-centro-west`](../maestros-m1a-centro-west/) |
| `AUD_CENTRO_ATENCION` | [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/) |

## Clarify

| # | Respuesta |
|---|-----------|
| 1 | Pipeline: padre centro gate-done. Perfil tile AG 10217 (flyout Centro Atención). |
| 2 | Alta nombre `(*)` + checkbox (default S) → Aceptar dialog → toast. Listado HAB. |
| 3 | DELETE físico (`desea_eliminar_grp_centro_atencion`). FK a centro → 409/toast. |
| 4 | Actor sin tile. |
| 5 | Sin TV/PDF/jobs. NextId `GRP_CENTRO_ATENCION` vía `sec_id_tabla`. |
| 6 | West / auditoría → fuera. No WAIVE. |
| 7 | G0 copy/validaciones. Chrome HAB (no west HIS). North input 500px. |
| 8 | Playwright `diferido(fixture)`. |

## Capacidades

| Capacidad | Legacy | Escritura | Este slice |
|-----------|--------|-----------|------------|
| ABM grp | `BBDatosGrpCentroAtencion` · 10217 | INSERT/UPDATE/DELETE | **In scope** |
| Buscar | `buscadorGrpCentroAtencion.xhtml` | Lee | **In scope** (absorbido) |
| Combo M1a | GET grupos | Lee | **ya portado** (no rehacer) |

## RF escritura

| RF | Crear | Visible | Fin |
|----|-------|---------|-----|
| RF-G1 | INSERT | GET/buscar | DELETE |

## NFR

Escritura p95 ≤ 1,5 s. Volumen `diferido(perf-volumen)` (medido en vacío relativo al dump). Concurrencia: NextId `id_grp_centro_ate`. Sin `FOR UPDATE` en el bean.

## Acceso

Menú 10217 bajo AG / Configuración Operativa / Centro Atención. Sin `Raise_application_error` de rol en el bean. Prueba actor sin tile.

## PL/SQL

Ninguna firma a portar. NextId reuso. Rareza HIS: `replaceAccentedChars` en el nombre; default `permiteElegirCentroTurnosBoolean=true`.

## Criterios

1. G0 inventarios. Chrome HAB.
2. **id** nacido del CU (no seed 99001).
3. Sin Flyway.
4. Playwright `diferido(fixture)`; NFR `diferido(perf-volumen)`; acceso actor sin tile.
