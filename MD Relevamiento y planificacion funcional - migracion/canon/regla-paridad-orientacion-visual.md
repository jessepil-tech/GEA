---
title: Regla — Paridad de orientación y visual (mapa mental + look)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.regla-paridad-orientacion-visual
---

# Regla — Paridad de orientación y visual

**Objetivo:** un usuario de HOSPITAL_2 se mueve en Hospital-Web **sin sentir que todo
cambió de lugar**. El look puede (y debe) ser el design system Angular. El **mapa
mental** no.

Complementa, no reemplaza:

- Chrome de *una* pantalla: [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md)
- Capacidad de negocio: [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md)
- Mapa M1/M2: [`arbol-mapeo-menu-legacy-web.md`](../relevamiento/arbol-mapeo-menu-legacy-web.md)
- Relevamiento HIS: [`relevamiento-his-orientacion/`](../relevamiento/relevamiento-his-orientacion/)

---

## Cuatro contratos

| | Duro (sin diferido/WAIVE no hay paridad de orientación) | Se adapta (DS / criterio) |
|--|----------------------------------------------------------|---------------------------|
| **A. Orientación** | Login → **grilla de módulos** (PNG + `DESCRIPCION`) → **entrar al módulo** → árbol del módulo = `MENU_APLICACION` (padre real, no carpeta xhtml) → pantalla | Title Case; filtro/búsqueda de módulos; agrupación visual opcional; tiles del DS; sidebar permanente **además** de la grilla |
| **B. Contexto de sesión** | Si legacy pide centro / recepción / box / call center **antes** de operar, el migrado también (o `diferido(slug)`) | Mismo paso, mejor layout; no esconderlo en un setting |
| **C. Pantalla** | [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) (copy, iconos, grilla, disabled, buscador) | Filtros en fila, botones compactos, componentes nativos |
| **D. Look Verona** | **No se clona** | Color, tipo, 12px, zoom, growl lejano, MAYÚSCULAS de grilla |

Test de producto: un operador dice “está en Turnos / Dominios / Habilitación…” y
**encuentra ese camino**. No: “ahora está en Configuración porque el archivo estaba ahí”.

---

## Contrato A — detalle

| Regla | Hacer | Prohibido |
|-------|--------|-----------|
| Fuente del árbol | `MENU_APLICACION` + perfil. Destino: Identity `GET /menus`. Catálogo TS solo mientras M2 | Inventar el padre por path `pages/configuracion/…` |
| Tile de la grilla | Si el módulo tiene al menos una pantalla: navegar al **inicio del módulo** (o al árbol del módulo). Los CU son **hijos** | `primaryRoute` = primer hijo enabled (cola, convenios, …) |
| Hojas no migradas | Visibles **disabled** en migración (“Pendiente…”). En prod: recorte por perfil como legacy | Silenciar el módulo entero; o mostrar 1.200 hojas enabled vacías |
| Iconos | PNG **solo en módulo**. Hoja = **texto** (Verona) | Lucide/`layers` en cada hoja |
| Satélites | AGI, display TV, Identity: **fuera** de la grilla HIS o grupo “Satélites” etiquetado | Mezclarlos como módulos del medio (RECEPCIÓN, HC, TURNOS) sin marca |
| ~1.200 ítems | No hardcode eterno en `hospital-menu.catalog.ts` | Copiar a mano el árbol completo en cada PR de CU |

**Landing vacío de módulo (Turnos Verona):** no es el modelo a copiar. El inicio
migrado puede ser picker de contexto (T1 call center) o un índice del árbol. Lo
prohibido es **saltar el módulo** y aterrizar en un CU suelto.

---

## Contrato B — detalle

Ejemplos vivos: Recepción (`inicioRecepcionCentro`: centro / recepción / box);
Turnos (call center en T1 / chrome “CONTACT CENTER…”).

Inventariar en el relevamiento del módulo (P5 / A2) y en Clarify del SDD. Gap →
`diferido(slug)`, no “después lo vemos”.

---

## Contrato D — qué no copiar (a11y / modernización permitida)

- `user-scalable=0`
- Error en growl lejos del campo
- Placeholder como único label
- MAYÚSCULAS en toda la grilla (mismo `DESCRIPCION`, Title Case)
- Iconos de topbar sin `title`/tooltip

El puesto clínico sigue siendo **desktop**. No es obligación hacer el HIS mobile-first.

---

## Clarify / spec (además de la tabla xhtml)

| Ítem | Evidencia | Migrado |
|------|-----------|---------|
| Padre de menú (`DESCRIPCION` padre + hoja) | `MENU_APLICACION` o Playwright | Mismo padre |
| ¿Gate de contexto? | xhtml inicio módulo | Mismo o `diferido(slug)` |
| ¿Satélite u HIS? | ¿Está en grilla BBModulos? | Misma familia |
| Tile / entrada | Click en inicio.xhtml | No deep-link al primer CU |

## Verify orientación (si el corte toca menú o home)

```markdown
## Paridad orientación
| Ítem | Estado | Nota |
|------|--------|------|
| Padre MENU_APLICACION | done / diferido / WAIVE | |
| Tile → módulo (no primer CU) | … | |
| Gate contexto | … | o N/A |
| Hoja texto (sin icono genérico) | … | |
```

## Reglas agente

- Mapa HIS: [`relevamiento-his-orientacion/`](../relevamiento/relevamiento-his-orientacion/)
- Corte shell Web (único): [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/)
- Pantalla: `regla-paridad-ui-legacy.md`
- Web: no colocar un CU bajo otro módulo porque “el xhtml estaba en esa carpeta”
