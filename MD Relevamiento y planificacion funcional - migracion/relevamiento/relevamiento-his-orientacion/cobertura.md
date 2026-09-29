---
title: Cobertura HIS — módulos (no % de hojas)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cobertura-his-modulos
---

# Cobertura HIS — por módulo

**No hay un porcentaje del 100% del HIS.** [`estado-piloto-vs-general.md`](../../estado/estado-piloto-vs-general.md)
inventaría cortes cobrados; el avance diario es
[`criterio-avance-e2e-datos.md`](../../canon/criterio-avance-e2e-datos.md) (E2E + data, no seed).

Denominador de **orientación:** módulos de la grilla `inicio.xhtml` (perfil) +
satélites. Hoy: **37 módulos** vistos con Playwright perfil **origin** (2026-08-31).
Faltan en esa grilla (O1): **SEGURIDAD** y **CRM** (perfil admin). El tile
`ADMINISTRACION_GENERAL_NA` **sí** está en origin (PNG: ADMINISTRACION GENERAL;
`ACCION` `/pages/configuracion/inicio`). FARMACIA en origin es el tile **DEPOSITO**.
AGI / Anunciador TV siguen siendo satélites (no están en `MENU_APLICACION` HOSPITAL).

Denominador de **capacidad:** **1240** ítems `MENU_APLICACION` (app HOSPITAL=`2`).
O1 los lista; el A–C por módulo los prioriza. Un % de hojas migradas ahora sería ficticio.

Leyenda código: **piloto/slice** = hay ruta Angular de un CU, no el módulo entero.

## Relevamiento A–C (capa 3 de negocio)

| Módulo / dominio | ¿A–C? | Notas |
|------------------|-------|--------|
| Anunciador + Tótem | **Sí** | [`relevamiento-node-anunciador/`](../relevamiento-node-anunciador/) |
| Turnos | **Sí (T0)** | [`relevamiento-turnos/`](../relevamiento-turnos/) |
| Nutrición | **Sí (T0)** | [`relevamiento-nutricion/`](../relevamiento-nutricion/) |
| Orientación HIS (mapa) | **Parcial** | esta carpeta; **O1 hecho** |
| Recepción (módulo completo) | **No** | Slices cola M1–M4; no A–C del módulo |
| Demanda espontánea | **No** | CU-C medidor |
| Configuración / catálogo (`ADMINISTRACION_GENERAL_NA`) | **Sí (T0)** | [`relevamiento-maestros/`](../relevamiento-maestros/) — 223 hojas; no spec del tile |
| Resto de la grilla origin (HC, internación, caja, …) | **No** | Solo nombre + PNG en catálogo Web |

**4 A–C de negocio en total:** Turnos + Nutrición + Administración General (**3 de 37** origin) + satélite
Anunciador (no es tile de la grilla). El resto no tiene A–C; las dependencias
**conocidas** (circuitos, troncos, huecos) están en
[`dependencias-modulos.md`](dependencias-modulos.md) — no sustituye A–C.
Capacidad de ingeniería (16 streams × BODY × backlog):
[`relevamiento-his-inventario-global/arbol-dependencias.md`](../relevamiento-his-inventario-global/arbol-dependencias.md)
§ Tablero.

Tamaño de legado vs avance (walk 14-sep-2026, cuatro denominadores, DAG de
streams): [`relevamiento-his-inventario-global/`](../relevamiento-his-inventario-global/).

## Grilla origin (37) — código vs mapa

| Módulo (origin) | Código Web (algo usable) | Mapa mental |
|-----------------|--------------------------|-------------|
| RECEPCIÓN | Cola + espera amb (piloto) | Tile salta a cola; sin gate puesto |
| TURNOS | T1 inicio CC + hab (bajo Config) | Árbol ~48 hojas no en sidebar |
| DEMANDA ESPONTANEA | CU-C medidor | |
| ATENCION MEDICA | `/ambulatoria/espera-atencion` (E3 cobrado) | |
| Los otros 33 | Tile pending o vacío | Visible en dashboard si `menuShowAll` |

Satélites (no en grilla origin): AGI recepción/espera, Anunciadores, Display TV,
Identity, Reports — **piloto avanzado**, otra familia de producto.

## Cómo se arma el roadmap

1. Filas = módulos (esta tabla) + satélites.
2. Por fila: A–C sí/no · gate contexto · 1.er CU · bloqueantes (Identity, maestros).
3. Orden = dependencia (como Turnos: personal → hab → agenda), **no** % de tiles verdes.
   Tablero de negocio: [`dependencias-modulos.md`](dependencias-modulos.md).
4. Capacidad / paralelo / BODY: tablero de 16 streams
   [`arbol-dependencias.md`](../relevamiento-his-inventario-global/arbol-dependencias.md).
5. `backlog-orden-*` elige **cuál** fila esta semana; no sustituye esta cobertura.

No hay “vamos 12%”. Hay: **piloto de plataforma + cuatro relevamientos de dominio
(Turnos, Nutrición, Administración General, Anunciador satélite) + 37 módulos origin (O1) + 2 tiles
solo admin (CRM, SEGURIDAD)**.
