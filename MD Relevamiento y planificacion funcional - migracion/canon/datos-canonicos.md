---
title: Datos canónicos del programa
status: canonical
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.datos-canonicos
indice: programa
indice_seccion: Programa y estado
indice_blurb: Registro de magnitudes — un dueño por cifra; los demás citan
---

# Datos canónicos

Capa transversal de [`gobierno-migracion.md`](gobierno-migracion.md).
Si un número del programa aparece en dos documentos con valores distintos,
**este registro manda** hasta que el dueño se remida.

## Cómo citar

Un documento que **no** es dueño de un dato no vuelve a escribir la cifra.
Enlaza acá o al dueño. Si la prosa necesita el número, se escribe **una vez**
y al lado el link.

No se cita el número de **versión** de una regla (`v1.10+`, `v1.14`), salvo
que el requisito sea literalmente esa versión. Se cita la regla y su sección.

No hay un «% del HIS». [`cobertura.md`](../relevamiento/relevamiento-his-orientacion/cobertura.md)
lo prohíbe; este registro tampoco lo publica.

## Magnitudes

Unidad y valor vigente. «Contraste» no se cita como si fuera el dueño.

| Magnitud | Valor | Unidad | Dueño | Cómo se remide | Contraste (no citar) |
|----------|------:|--------|-------|----------------|----------------------|
| Filas `MENU_APLICACION` app HOSPITAL=`2` | **1240** | filas catálogo (carpetas + hojas + separadores) | [`dump-menu.md`](../relevamiento/relevamiento-his-orientacion/dump-menu.md) | `SELECT` O1 sobre Oracle `TS.MENU_APLICACION` | ~1.200 en prosa suelta |
| Hojas con `ACCION` (misma app) | **1032** | hojas (no carpetas) | [`inventario.md`](../relevamiento/relevamiento-his-inventario-global/inventario.md) | Walk del dump: filas con `ACCION` no vacía | 935 / 941 xhtml `in_menu` del índice generado |
| Nodos menú perfil origin | **1199** | nodos `f_get_menu_acceso_all` | [`dump-menu.md`](../relevamiento/relevamiento-his-orientacion/dump-menu.md) | dump O1 2026-08-31 | — |
| Tiles grilla origin | **37** | módulos `inicio.xhtml` | [`dump-menu.md`](../relevamiento/relevamiento-his-orientacion/dump-menu.md) | SP módulos origin | cobertura usa este 37 |
| Relevamiento A–C de negocio | **2 de 37** origin + satélite Anunciador | módulos con pipeline+maestros | [`cobertura.md`](../relevamiento/relevamiento-his-orientacion/cobertura.md) | contar filas A–C en esa tabla | «3/37» sin decir que Anunciador no es tile |
| xhtml (9 WAR) | **3347** | archivos | [`inventario.md`](../relevamiento/relevamiento-his-inventario-global/inventario.md) | walk `Hospital-Legacy` | índice generado: 3347 (coincide) |
| Java WAR+BUSINESS | **9458** | archivos | [`inventario.md`](../relevamiento/relevamiento-his-inventario-global/inventario.md) | walk 14-sep-2026 | índice generado **9462** (otro día) |
| Beans `BB*` | **2510** | archivos | [`inventario.md`](../relevamiento/relevamiento-his-inventario-global/inventario.md) | mismo walk | 2460 referenciados desde xhtml (índice) |
| Diseños BIRT únicos (bruto) | **329** | basename | [`inventario.md`](../relevamiento/relevamiento-his-inventario-global/inventario.md) | 408 archivos / copias entre WAR | listado legacy |
| Universo BIRT a migrar (base+GEA) | **297** | basename | [`listado-reportes-birt-otro-cliente.md`](../relevamiento/listado-reportes-birt-otro-cliente.md) | 329 − 32 N/A otro cliente (53 archivos) | 408 archivos brutos |
| Reportes BIRT **con caller** | **231** | basename invocados | [`inventario.md`](../relevamiento/relevamiento-his-inventario-global/inventario.md) | string `Nombre.rptdesign` en java/xhtml/xml | índice generado **250** |
| Sidecar BIRT registrados | **37** | diseños en PG | [`README inventario global`](../relevamiento/relevamiento-his-inventario-global/README.md) | `hospital.reports.registered` | avanza por corte R5 |
| Package BODY Oracle | **326** | bodies | [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md) | captura `package_body/` | walk 14-sep dijo 325 / 61 negocio |
| BODY de dominio | **62** | packages no `TBL_AUD_*` | [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md) | 326 − 264 | inventario 61 |
| Tablas con historial campo a campo | **264** | tablas = packages `TBL_AUD_*` (1:1) | [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md) | contar `TBL_AUD_*` | no es «264 packages de negocio» |
| Triggers `TAUD_*` | **1419** (264 llaman package; **1155** solo `fecha_last_update`) | triggers | [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md) | captura triggers | sello ≠ historial |
| Jobs Quartz del HIS | **82** (67 en `SCHEDULER` + 15 en otros proyectos) | clases job | [`relevamiento-procesos-programados/`](../relevamiento/relevamiento-procesos-programados/) | `indice-legacy.sh` → `job.tsv` | el proyecto SCHEDULER está fuera de alcance; las capacidades no |
| Streams del tablero | **16** | dominios con BODY | [`arbol-dependencias.md`](../relevamiento/relevamiento-his-inventario-global/arbol-dependencias.md) § Tablero | filas del tablero | carril ≠ stream ([`glosario`](glosario-migracion.md)) |
| Cortes abiertos en `docs/cortes/` | **69** | slugs | [`cortes/README.md`](../cortes/README.md) | `find docs/cortes/*/* -type d` | el linter cuenta cortes + spikes + relevamientos |
| Clientes `esClienteX()` | **16** clientes · **313** invocaciones | ramas en código | [`regla-instalacion-referencia.md`](regla-instalacion-referencia.md) | grep `esCliente` en `Hospital-Legacy` | repo local = `cliente="TS"` (todas apagadas) |

## Status de documento (P3)

Vocabulario cerrado en el front-matter. El linter lee `status:`, no la negrita del H1.

| `status` | Significa |
|----------|-----------|
| `canonical` | Canon de proceso o regla |
| `active` | Vigente (corte, snapshot, backlog) |
| `draft` | En redacción; no se cobra |
| `generated` | Lo escribe una herramienta; no editar a mano |
| `replaced` | Puntero; el contenido está en `reemplazado_por` |
| `proposed` | Propuesta aún no ejecutada del todo |

Prohibido el `done` huérfano junto a `gate-done`. El **gate** es estado del corte
([`glosario`](glosario-migracion.md)); el **status** es estado del documento.

Campos de vigencia: `reemplaza_a` · `reemplazado_por`.
