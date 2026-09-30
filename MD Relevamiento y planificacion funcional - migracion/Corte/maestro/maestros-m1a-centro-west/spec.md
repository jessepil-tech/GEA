---
title: Spec — M1a logos pack del centro
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro-west.spec
---

# Spec — M1a logos centro

Padre: [`maestros-m1a-centro/`](../maestros-m1a-centro/) · menú **10203** (acción Logos sobre fila existente; no hay hoja de menú nueva).

## Problema

M1a firmó datos + buscador. Gatillo west: `logoCentroAte.xhtml` (7 slots BYTEA en `ts.pack_logos` + `centro_atencion.id_pack_logos`). El west completo supera 8 xhtml → **partir**.

## Resultado

HAB: en el listado de centros, acción Logos → dialog 7 filas (preview 32px, fileUpload, trash confirm, extensión). Persistencia `updatePackLogos` + toast `ROW_UPDATE_INFO`. Evidencia = `id_pack_logos` / `id=` nacido del CU (no seed).

## Universo firmado

Índice 2026-09-18 `indice-legacy.sh --semilla logoCentroAte`:

- 2 xhtml / 2 beans / 0 PL/SQL · techo **ok**.
- `--jobs logo`: 3 hits ANMAT (otro circuito) → **N/A**.
- `--jobs centro_atencion`: vacío → **N/A**.

| Entra | Path |
|-------|------|
| `logoCentroAte.xhtml` | 7 slots + Aceptar south |
| `BBLogoCentroAte` | session centro; listeners; `updatePackLogos` |
| `PackLogos` + `ImpBusPackLogos.update` | insert/update + link `id_pack_logos` del centro |

| Fuera | Destino |
|-------|---------|
| `centroAtencion.xhtml` resto west | [`maestros-m1a-centro-west-resto`](../maestros-m1a-centro-west-resto/) |
| `servicioPorCentro` | M1b (`servicio_centro`) **N/A** este slug |
| pack Empresa / PtoVta / Param HC | mismo `ImpBusPackLogos` otras ramas · **N/A** |
| `AUD_CENTRO_ATENCION` | [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/) |

## Clarify

| # | Respuesta |
|---|-----------|
| 1 | Pipeline: padre centro gate-done. Perfil tile AG 10203. |
| 2 | 7 logos; SVG app/term-auto; PNG resto. Aceptar persiste. |
| 3 | Trash confirma `desea_eliminar_el_icono`; null hasta Aceptar (HAB: DELETE slot + Aceptar). |
| 4 | Actor sin tile 10203. |
| 5 | Sin TV/PDF/jobs. NextId `PACK_LOGOS` vía `sec_id_tabla`. |
| 6 | Resto west / auditoría / pack facturación → fuera. No WAIVE. |
| 7 | G0 copy/validaciones. Chrome: accordion west HIS 170px (`app-his-west-accordion`, paridad agenda/horarios). Logo en main, no ícono de grilla. Preview h=32. |
| 8 | Playwright `diferido(fixture)`. |

## Capacidades

| Capacidad | Legacy | Escritura | Este slice |
|-----------|--------|-----------|------------|
| Pack logos centro | `BBLogoCentroAte` | INSERT/UPDATE `pack_logos` + UPDATE FK centro | **In scope** |
| 7 slots | xhtml | BYTEA | **In scope** |
| Pack empresa/pto/HC | mismas firmas otras pantallas | — | **N/A** |

## RF escritura

| RF | Crear | Visible | Fin |
|----|-------|---------|-----|
| RF-L1 | PUT slot que dispara insert HIS | GET meta / GET bytes | DELETE slot (null BYTEA; fila pack se conserva) |

## NFR

Escritura p95 ≤ 1,5 s. Volumen `diferido(perf-volumen)` (dump `pack_logos` COUNT 0 al abrir). Concurrencia: last-write-wins BYTEA (HIS sin `FOR UPDATE`). Rollback: PUT falla → sin FK huérfana (misma transacción insert+link).

## Acceso

Menú 10203. Sin `Raise_application_error` de rol en el bean. Prueba actor sin tile.

## PL/SQL

Ninguna firma a portar. NextId reuso.

Rarezas HIS (invariantes):

- Insert de pack **solo** si hay `logoApp` / `logoTermAutoRecep` / `logoImpresion` / `logoImpresionSmall` / `logoCompInt`. `logoPrescripciones` y `logoHc` **no** crean pack.
- `ERROR_TAMANO_IMG` / `MAX_SIZE` placeholders `{1}` `{2}` (1-based).
- PNG `ImageIO` 215×120 o 50×45 (small). SVG sin check de pixels.
- Aceptar siempre toast `ROW_UPDATE_INFO` aunque no haya pack insertable.

## Criterios

1. G0 inventarios. Chrome HAB en listado 10203.
2. **id** de `pack_logos` nacido del CU.
3. Sin Flyway. Sin seed de filas `pack_logos`.
4. Playwright `diferido(fixture)`; NFR `diferido(perf-volumen)`; acceso actor sin tile.
