---
title: Inventario global HIS — migrado vs legado
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.inventario-global-his
---

# Inventario global HIS — migrado vs legado

Medición de **tamaño del legado** y **avance por carril** (no un % único del HIS).
Walk de código **14-sep-2026**. Delta BIRT del mismo día:
`FormularioPedidoMedicamentos` en sidecar (**37** diseños registrados).

## Veredicto (una frase)

La **plataforma** (Identity, schema `ts`, sidecar BIRT, PoC ECS) está lista.
El único carril de artefactos con masa crítica es **BIRT (~16%** de los
reportes que el HIS **invoca**). El HIS clínico/administrativo
(**37** tiles origin · **1.032** hojas con `ACCION` · **1.240** filas catálogo —
[`datos-canonicos.md`](../../canon/datos-canonicos.md)) está en **piloto + Turnos
documentado**, no en cobertura de producto. Completar “la solución” es un
programa de **años**, no el cierre de 329 `.rptdesign`.

## Quick path

1. Leer este README (denominadores + scorecard).
2. Tablas de objetos: [`inventario.md`](inventario.md).
3. Streams, paralelo y **tablero 16 × backlog:** [`arbol-dependencias.md`](arbol-dependencias.md).
4. Planificar: un **stream** (módulo + beans + BODY + familia BIRT), no un xhtml.
   Esta semana: [`backlog-orden-2026-08-14.md`](../../planificacion/backlog-orden-2026-08-14.md).

## Cuatro denominadores (prohibido un % único)

| Denominador | Universo | Hecho (este walk + delta BIRT) | Cómo leerlo |
|-------------|----------|--------------------------------|-------------|
| Reportes **con caller** | 231 | **37** en sidecar (~16%) | Listado + `hospital.reports.registered` |
| Diseños únicos (incluye huérfanos) | 329 | 37 sidecar + 98 sin caller | No migrar huérfanos salvo pedido |
| Módulos grilla origin | 37 | **3** con A–C de negocio | Turnos + Nutrición + Administración General. Satélite Anunciador = 4.º A–C (no es tile origin) |
| Hojas `MENU_APLICACION` (HOSPITAL=2) | 1.032 | ~10 rutas en clone Web 18-ago | SDD Turnos T2–T5.3 **no** está en ese clone |

Canon: [`cobertura.md`](../relevamiento-his-orientacion/cobertura.md) ·
[`gobierno-migracion.md`](../../canon/gobierno-migracion.md).

## Scorecard 14-sep-2026

| Carril | Estado | Último corte útil |
|--------|--------|-------------------|
| Sidecar BIRT + PoC ECS | Motor genérico; 37 diseños PG; pool workers | FormularioPedidoMedicamentos 14-sep |
| Piloto AGI / recepción / TV | Gate-done G1…CU-C; cola M1–M4 | Cierre P0; P1–P5 abiertos |
| Turnos (SDD) | T2–T5.4-b + T6.1 gate-done en docs; T6 padre diferido | T5.4-b 14-sep — **desfasaje** Web/API vs clone 18-ago |
| Hospital-Web / API (clone `origin/dev/dev`) | Último commit **18-ago-2026**. Sin `/turnos/*` | No usar este clone como evidencia T5.3 |
| Identity + schema `ts` | Oleada A + cutover Api V27–V33 | Ago 2026 |

## Regenerar números

Scanners (artefacto local `Hospital-Reports/target/preview/`, gitignored):

- `Hospital-Reports/tools/_inventory_solucion.py`
- `Hospital-Reports/tools/_inventory_compact.py`
- `Hospital-Reports/tools/_dep_graph.py`

Canvas Cursor (no es SoT): `inventario-migracion-his` · `arbol-dependencias-equipo`.
**SoT de planificación = esta carpeta.**

## Checklist de lectura

- [ ] No se cita un “% del HIS” sin decir el denominador.
- [ ] BIRT se planifica sobre **231 usados**, no 329 archivos.
- [ ] UI se planifica con los denominadores de [`datos-canonicos.md`](../../canon/datos-canonicos.md) (hojas con `ACCION` ≠ filas catálogo ≠ tiles origin), no 3.347 xhtml.
- [ ] Packages se portan **on-demand** (CU o `{call}` del diseño), no los 264 `TBL_AUD_*`.
- [ ] Capacidad de equipo = tablero de 16 streams; fila de **esta semana** = backlog.
