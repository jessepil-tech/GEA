---
title: Spec — Paridad orientación Hospital-Web
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.paridad-orientacion-web
---

# Spec — Paridad orientación Web

Capa 3: [`relevamiento-his-orientacion/`](../../../relevamiento/relevamiento-his-orientacion/) · O1
[`dump-menu.md`](../../../relevamiento/relevamiento-his-orientacion/dump-menu.md).  
Regla: [`regla-paridad-orientacion-visual.md`](../../../canon/regla-paridad-orientacion-visual.md).  
Código hoy: `Hospital-Web/.../hospital-menu.catalog.ts`, `dashboard.component.ts`.

## Problema

M1 armó grilla PNG + sidebar, pero el **mapa mental** no es Verona:

| Hoy | Legacy (O1 / Playwright origin) |
|-----|----------------------------------|
| `primaryRoute` = primer hijo enabled | Tile → **inicio del módulo** |
| Hab serv/prof bajo grupo `CONFIGURACION` | `ATENCION_TURNOS` → dominios → **15411 / 15412** |
| TURNOS children = solo `/turnos/inicio` | 4 raíces (turnero, paciente, consultas, dominios) |
| AGI + ANUNCIADOR tiles HIS | No están en `MENU_APLICACION` HOSPITAL |
| Hoja `icon: 'layers'` | Hoja = texto |
| Grupo `CONFIGURACION` + tile `ADMINISTRACIÓN GENERAL` duplicado | Un módulo: `ADMINISTRACION_GENERAL_NA` (id **10000**) |

La URL Angular **puede** seguir en `/configuracion/hab-turnos-*`. El **padre de menú**
no es el path.

## Resultado esperado

1. Tile HIS → `moduleEntryRoute` (inicio de módulo), **nunca** el primer CU.
2. Hab serv/pers (y hoja equipo pending) bajo **TURNOS / Dominios** (ítems 15411/15412;
   15413 pending = D-TUR-12).
3. TURNOS: Inicio (T1) + raíces O1 visibles; hojas sin Angular **disabled**.
4. AGI y Anunciador: **fuera de la grilla HIS** o grupo sidebar **Satélites** (no al
   lado de RECEPCIÓN como un módulo más). Display TV sigue URL aparte.
5. Hojas del sidebar **sin** icono genérico.
6. Un solo módulo de configuración HIS: `menuKey` `ADMINISTRACION_GENERAL_NA`;
   Convenios quedan **hijos** de ese módulo (tile no va a Convenios).
7. RECEPCIÓN: tile → `/recepcion/inicio` (índice del módulo). Gate puesto → hijo.

## Clarify — **propuesto** (listo para código; 2026-08-31)

| # | Pregunta | Respuesta | Evidencia |
|---|----------|-----------|-----------|
| 1 | ¿Este slug se reabre por módulo? | **No.** Un corte de shell. CUs posteriores solo **cumplen** padre O1. | gobierno 1b / 3b |
| 2 | ¿Tile = primer hijo enabled? | **No.** Campo `moduleEntryRoute` (o equivalente) por módulo. Sin inicio Angular y sin índice: tile **no navega** (como HC hoy). | `BBModulos` → `inicio*.faces` |
| 3 | ¿Hab bajo Configuración? | **No.** Padre = TURNOS Dominios **15411/15412**. Ruta HTTP puede quedarse. Copia **10811** bajo Admin General **no** se arma en este corte (catálogo completo = M2). | O1 dump |
| 4 | ¿Árbol TURNOS 48 hojas? | **No.** Raíces + hojas ya migradas (inicio, hab). Resto pending disabled. | MenuBuilder; inventario Playwright |
| 5 | ¿AGI/TV en grilla HIS? | **No.** Grupo Satélites (sidebar) + **excluídos** de `buildHospitalModuleTiles`. | BBModulos origin 37 |
| 6 | ¿Gate Recepción en este slug? | **No** — **diferido(`paridad-recepcion-gate`)**. Índice `/recepcion/inicio` **sí** (no aterrizar en cola). | `inicioRecepcionCentro` |
| 7 | ¿Identity / 1240 filas? | **No.** Catálogo TS piloto. M2 aparte. | arbol-mapeo M2 |
| 8 | ¿Look Verona? | **WAIVE** contrato D. Title Case en labels OK. | regla orientación |
| 9 | ¿Sidebar anidado Dominios? | **v1 un nivel:** hojas hab como hijos de TURNOS (padre mental = Dominios). Submenu anidado = fuera (chrome M2). | `sidebar-nav` 1 nivel |
| 10 | ¿Prune tiles Web que no están en O1 (FARMACIA, RECETAS, …)? | **Fuera** este corte. No ensanchar. | dump origin 37 + CRM/SEGURIDAD admin |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Grilla módulos PNG + DESCRIPCION | `inicio.xhtml` / BBModulos | Conservar; corregir destino del click |
| Tile entra al módulo | Click módulo → `inicio*.faces` | **In scope** |
| Árbol padre = MENU_APLICACION | MenuBuilder; O1 15411 | **In scope** (hab + raíces TURNOS) |
| Hab serv/prof bajo TURNOS/Dominios | dump 15411/15412 | **In scope** |
| Hab equipo | 15413 | **pending** (D-TUR-12; visible disabled) |
| Copia hab bajo Admin General 10811 | dump 10811 | **Fuera** (M2) |
| 4 raíces TURNOS | O1 `ATENCION_TURNOS` | **In scope** (inicio enabled; resto pending) |
| Satélites fuera de grilla HIS | AGI.war / anunciadorVue | **In scope** |
| Hoja = texto (sin icono) | Verona `pv:menu` | **In scope** |
| Gate call center Turnos | `inicioTurnos` | **N/A** (T1 hecho) |
| Gate puesto Recepción | `inicioRecepcionCentro` | **diferido(`paridad-recepcion-gate`)** |
| Recorte perfil origin (37 vs 39) | SP + perfil 67 vs 1 | **Fuera** (`menuShowAll` / M2) |
| Convenios ABM | CU-A | No se mueve de barrio: hijo de Admin General, no destino del tile |
| Look Verona | contrato D | **WAIVE** |
| GET /menus Identity | contrato identidad §6 | **Fuera** (M2) |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | `moduleEntryRoute` (o campo equivalente) en el catálogo. `buildHospitalModuleTiles` **no** usa el primer hijo enabled. Tests: RECEPCIÓN ≠ `/recepcion/cola`; TURNOS = `/turnos/inicio`; Admin General ≠ Convenios; HC sin ruta. |
| RF-2 | RECEPCIÓN: página índice `/recepcion/inicio` (enlaces a cola / espera; sin gate). Tile → esa ruta. |
| RF-3 | TURNOS: hijos Inicio + Dominios (hab serv/pers enabled, hab equipo disabled) + turnero / paciente / consultas pending. Quitar hab del grupo Configuración. |
| RF-4 | Un módulo HIS configuración: label Administración general, `menuKey` `ADMINISTRACION_GENERAL_NA`. Convenios (y resto config ya migrado) como hijos. Eliminar el duplicado `CONFIGURACION` / segundo tile Admin General. Tile → índice config **o** no navega si no hay inicio Angular (no Convenios). |
| RF-5 | AGI + ANUNCIADOR: grupo sidebar `Satélites` (u homónimo acordado); **no** entran a `buildHospitalModuleTiles`. Display TV sin cambio de URL. |
| RF-6 | `leaf()` / `pending()`: **sin** `icon: 'layers'`. PNG solo en módulo. |
| RF-7 | IT catálogo + smoke dashboard/sidebar (Paridad orientación en verify). |
| RF-8 | Actualizar [`mapa-menu-hospital-web.md`](../../../relevamiento/mapa-menu-hospital-web.md) y tests `sidebar-nav.config.spec.ts`. |

## Fuera de alcance

- Flyway / Api / Identity.
- Implementar agenda, consultas turnos, hab equipo, gate recepción.
- Sincronizar las ~1240 hojas ni borrar tiles extra (FARMACIA, RECETAS, …).
- Deep-link a legacy.

## Criterios de aceptación

1. Click tile RECEPCIÓN → `/recepcion/inicio`, no cola. Desde el índice se llega a cola.
2. Click tile TURNOS → `/turnos/inicio`. En sidebar: hab serv/pers bajo TURNOS; no bajo Configuración.
3. Grilla home **sin** tiles AGI ni ANUNCIADOR; sí accesibles en Satélites (sidebar).
4. Hojas del menú lateral sin icono Lucide/`layers`.
5. Verify: ninguna fila del inventario en silencio.

## Deudas / hijos

| Id | Nota |
|----|------|
| **`paridad-recepcion-gate`** | Gate centro / recepción / box **antes** de operar cola. Carpeta hija. Cobrar antes de otro módulo HIS (o entra al backlog de la semana). |
| M2 Identity menús | `GET /menus` + sync TS → PG. No este slug. |
| D-TUR-12 | Hab equipo (hoja pending aquí). |
| Copia 10811 | Segunda ubicación hab en Admin General — con catálogo M2. |
