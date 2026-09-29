---
title: Relevamiento — orientación HIS (menú, home, contexto)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.relevamiento-his-orientacion
---

# Relevamiento — orientación del HIS

`phase_id:` **`sdd.hospital.relevamiento-his-orientacion`**  
Fecha: **2026-08-31**  
**Estado:** **parcial** — reglas + **O1 hecho** (`dump-menu.md`, Oracle 11.2 vía VPN
`127.0.0.1:1521`). Playwright perfil **origin** (login `prueba1`) coincide: **37** tiles.

Esto es capa 3 de **mapa mental** (todo el HOSPITAL_2). **No** reemplaza
[`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md) por módulo
(Turnos, Recepción, HC, …). Un dump de menú no cierra maestros ni ABM.

**Reglas:** [`regla-paridad-orientacion-visual.md`](../../canon/regla-paridad-orientacion-visual.md).

## Veredicto (Fase E, 2026-08-31)

| | |
|--|--|
| Viable | Sí: el modelo destino ya estaba en [`arbol-mapeo-menu-legacy-web.md`](../arbol-mapeo-menu-legacy-web.md) |
| Bloqueante de “el usuario no se pierde” | Tile Web → primer CU; hab turnos bajo CONFIGURACIÓN; Recepción sin gate de puesto; AGI/TV como módulos HIS |
| Primer corte de código | SDD [`paridad-orientacion-web/`](../../cortes/plataforma/paridad-orientacion-web/) **gate-done** (W6); hijo [`paridad-recepcion-gate/`](../../cortes/recepcion/paridad-recepcion-gate/) |
| No hacer | Relevamiento A–C de los 37 módulos en un solo paquete |

## Profundidad de análisis

Mapa mental del HIS (tiles / menú / circuitos). **No** cierra el grafo de un
módulo: `dependencias-modulos.md` se declara liviano a propósito.

| Capa | Qué cerraría | Estado | Evidencia |
|------|--------------|--------|-----------|
| Pipeline A1–A9 | Recorrido de orientación (no pipeline de negocio) | cerrado | [`pipeline.md`](pipeline.md) — A2b de menú, no A1–A9 de un CU |
| Escritores cruzados | Quién escribe en cada tile | muestra | [`dependencias-modulos.md`](dependencias-modulos.md): inferido / hueco salvo Turnos y Nutrición |
| Procesos programados | Cada job se porta / difiere / N/A | N/A | No es un dominio; jobs en [`relevamiento-procesos-programados/`](../relevamiento-procesos-programados/) |
| Firmas del package | Universo de un BODY | N/A | El mapa no porta packages; 16 streams en `arbol-dependencias.md` |
| Integridad referencial | FK del HIS | N/A | Orientación de menú, no schema |
| Reportes e integraciones | 329 `.rptdesign` / WS | muestra | Inventario global cuenta reportes; no es caller graph por módulo |

## Qué es “relevar todo el HIS” aquí

1. **O1 — Árbol** — `MENU_APLICACION` (aplicación HOSPITAL) + 1–2 perfiles (origin + un
   admin de configuración). Entrega: lista módulos + padres + `ACCION`.
2. **O2 — Gates** — pantallas de contexto (centro/puesto/call center) por módulo.
3. **O3 — Satélites** — qué **no** está en la grilla HIS (AGI.war, anunciadorVue). `SEGURIDAD` y `CRM` **sí** están en `MENU_APLICACION`; origin no los ve.
4. Recién entonces, **por módulo**, el relevamiento A–C de siempre cuando se migre
   negocio (Turnos ya tiene T0).

O1: [dump-menu.md](dump-menu.md) (JDBC `ojdbc7`, `Hospital-Legacy/tools/relevamiento/DumpMenuO1.java`).

## Índice

| Archivo | Rol |
|---------|-----|
| [pipeline.md](pipeline.md) | Recorrido orientación (no pipeline de negocio) |
| [inventario.md](inventario.md) | Módulos origin + desvíos Web |
| [maestros.md](maestros.md) | Menú / perfiles / Identity |
| [matriz.md](matriz.md) | Capacidades de orientación |
| [cobertura.md](cobertura.md) | **% HIS:** no existe aún; scorecard por módulo (37 origin) |
| [Inventario global](../relevamiento-his-inventario-global/) | Walk 14-sep: objetos, BIRT, DAG de streams (no sustituye A–C) |
| [dependencias-modulos.md](dependencias-modulos.md) | Roadmap: circuitos + dependencias conocidas (**no** 37 A–C) |
| [dump-menu.md](dump-menu.md) | **O1** catálogo + origin + admin (CRM/SEGURIDAD) |
| [cortes.md](cortes.md) | O1–O3 + SDD Web |

## Relación con otros docs

- Mapa vivo de rutas piloto: [`mapa-menu-hospital-web.md`](../mapa-menu-hospital-web.md)
- Plan M1/M2 (API menús): [`arbol-mapeo-menu-legacy-web.md`](../arbol-mapeo-menu-legacy-web.md)
